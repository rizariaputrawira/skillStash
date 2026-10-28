# CAS workflow branches

Read the relevant section when connecting through a specific authentication route, transferring data, managing table lifetime, or validating an existing table. Examples are original, source-checked against the editions in [../SOURCES.md](../SOURCES.md), and **not executed against CAS**. Adapt endpoints, names, permissions, schema, and workload bounds to the assignment; these examples do not authorize execution.

## Connection and authentication branches

### OAuth over HTTPS

Use the actual CAS HTTPS endpoint supplied by the deployment, including any required proxy base path. The token must already be acquired through an approved identity flow; this example neither registers an OAuth client nor implements a token service.

```python
import os
import swat

# Inject these outside source control through the approved runtime mechanism.
# CAS_URL must be the approved HTTPS CAS endpoint, not just a Viya home URL.
# CAS_TOKEN is a secret. Configure CA trust before creating the connection.
conn = swat.CAS(os.environ["CAS_URL"], password=os.environ["CAS_TOKEN"])
try:
    status = conn.retrieve("builtins.serverStatus")
    if status.severity != 0:
        raise RuntimeError("CAS status requires review of sanitized diagnostics")
    server_build = status["About"]["VersionLong"]
    # Perform only the authorized workflow here; do not print the full About result.
finally:
    conn.terminate()  # This example creates and owns a new session.
```

Resolve ambient credential configuration first: an inherited `CAS_USER`/username or an authinfo match can select an unintended authentication route. Do not print the environment, URL query credentials, token, or connection representation. Environment injection is a delivery example, not a guarantee of secret isolation; use the site's credential facility and restrict access to the process environment.

SWAT's documented OAuth convention is `password=access_token` with no username. Do not turn a bearer token into a Basic-auth username/password pair. Expired tokens, wrong audience/identity configuration, inability to create a CAS session, and lack of table authorization are different failures. Acquire/renew tokens through the approved identity workflow rather than logging them or adding speculative authentication fallbacks.

### Authorization code and PKCE

For an interactive, approved HTTP(S) authorization-code workflow, the 1.18.1 documentation exposes `authcode=` and `pkce=True`. PKCE was added in SWAT 1.14.0. With `pkce=True`, omit `authcode=`; the documented flow supplies a URL and prompts for the code. Do not use a human-prompt flow in an unattended job. Do not assume that a client-side option establishes server-side OAuth registration or that historical Python minimums quoted for that feature override the selected package's installation requirements.

### Authinfo and Kerberos

- For an approved user/password route, use `swat.CAS(host, port, protocol="cas", authinfo=protected_file)` instead of source literals. The documented authinfo match uses the supplied hostname exactly, not DNS expansion; inspect the selected route without revealing file contents. On POSIX the file must be owner-readable/writable only (`0600`). The observed 1.18.1 getting-started page says user/password authentication will be deprecated in a future release; it does not say it has already been removed.
- Kerberos is **binary-only** in the observed SWAT documentation. Check the ticket, service principal, hostname, and deployment policy rather than supplying empty password guesses.
- **Viya 3.5-specific:** SWAT 1.8.0 and later use the modern Viya service-principal convention by default. The documented override for a 3.5 CAS target is `CASSPN=sascas@<actual-hostname>`, set before connecting. Do not apply this legacy override indiscriminately to modern platform deployments.

### Protocol/TLS failure triage

1. Separate import/native-library failure from failure to contact the supplied endpoint. The observed 1.18.1 install page lists 64-bit Python 3.7–3.14 for Linux/Windows binary use, and calls out Linux `libnuma.so.1`. Treat this as a documentation observation, not a complete artifact/platform support matrix; inspect the selected release's actual artifact and dependencies.
2. Confirm endpoint/protocol agreement: `protocol="cas"` requires a binary port; HTTPS requires the CAS HTTP service and correct proxy route. Do not infer correctness from a familiar port number or a successful Viya browser login.
3. Fix trust and hostname mismatch using the approved CA chain. `CAS_CLIENT_SSL_CA_LIST` is a local PEM path in the documented Linux/macOS and Windows REST cases; Windows binary uses its certificate store. Keep validation enabled. Do not modify system libraries, trust stores, ingress, or server configuration without authorization.
4. Once transport and authentication work, run severity-checked `builtins.serverStatus`, then the specific caslib/table check. Record sanitized failure stage and diagnostics. Do not turn authorization denial into a request to create a new caslib or broaden privileges.

## Data location and transfer

| Intent/API | Where the source is read or parsed | Transfer and operational consequence |
| --- | --- | --- |
| `conn.read_csv(client_path_or_url, ...)` | pandas parses on the Python client | Parsed data is uploaded into CAS. A URL first reaches the client; it is not a CAS-side URL load. Bound client memory and source size. |
| `conn.upload_frame(local_dataframe, casout=...)` | Data already occupies client memory | Uploads client data to CAS; agree destination and schema conversions before transfer. |
| `conn.upload_file(client_path_or_url, ...)` | Client reads/downloads bytes; CAS parses the uploaded file | Does not eliminate the client-to-CAS transfer. Avoid remote sample URLs in production recipes. |
| `conn.load_path(relative_path_or_resource, caslib=..., casout=...)` | CAS reads a source available through its caslib | A file path is relative to that caslib's data source, not the notebook's current directory. For DB connectors the resource can be a database table instead of a file. |
| `conn.retrieve("table.loadTable", caslib=..., path=..., casOut=...)` | CAS-side load action | Returns `CASResults`, allowing explicit severity checks; inspect the reflected action parameters and connector-specific import options. |
| `conn.CASTable(name, caslib=...)` | Constructs a client reference | Does not load, copy, or prove existence of the table. Keep the connection alive. |
| `table.fetch`, `tbl.head(n)` | CAS selects rows; client receives result data | Use explicit columns and row cap. Without an explicit order, do not treat repeated samples as stable pagination. Sorting can be expensive even when the output is tiny. |
| `tbl.to_frame()` | All referenced rows move to a local DataFrame | Do not use as a health check or on an unbounded production table. Estimate size and get authorization for a full export. |
| CAS aggregates, e.g. `simple.summary` | Calculation in CAS; summary results usually return locally | Limit selected inputs and group cardinality. A grouped summary can still be large; use action-supported server output where needed. |

The binary protocol supports data message handlers for specialized client-side loaders; REST does not. Do not silently substitute a REST connection for a binary-only loading design. Validate imported column types/lengths, missing-value handling, encoding, dates/times, identifiers, and expected counts rather than assuming pandas and CAS types are identical. Fetching a few rows cannot demonstrate that all long strings or large integers were preserved; use targeted server-side checks appropriate to the data and release. In particular, modern action docs contain release-specific BIGINT handling; do not transplant those options to Viya 3.5 without matching documentation and reflection.

If loading is authorized, use an explicit, task-owned scratch name and caslib, with replacement disabled. For example, after inspecting `table.loadTable`:

```python
loaded = checked(conn, "table.loadTable",
                 caslib=source_caslib, path=relative_source,
                 casOut={"name": scratch_name, "caslib": scratch_caslib,
                         "replace": False, "promote": False})
```

Here `checked` is defined below; names and relative source come from the authorized task. Then check the resulting table with existence, schema, and count actions. Convenience loaders returning a `CASTable` do not provide the same direct result object; use explicit follow-up outcome checks even if the loader returned normally.

## Table lifecycle

Treat these as independent state transitions, not synonyms:

| State | How to request it | What proves the outcome |
| --- | --- | --- |
| Session table | Load/create in the owned session with promotion off | `table.tableExists` returns `exists == 1` for the explicit caslib/name; schema/count meet the task. It is not available to an unrelated session simply because the caslib is global. |
| Promoted/global in-memory table | Authorized `table.promote`, or an action's documented promotion option | `exists == 2`, correct destination caslib/name, and appropriate `TableInfo` scope. If cross-session visibility is a requirement, check from another authorized session; global does not mean public. |
| Saved backing-store object | Authorized `table.save` to a supported caslib data source | Severity plus returned destination; use targeted `table.fileInfo`/backing-source evidence to check the saved object. A `table.tableExists` check alone proves in-memory state, not persistence. |
| Reloaded saved data | Authorized `table.loadTable` into a new, non-replacing scratch name | Recheck expected schema/count or a task-specific aggregate. A round-trip test is an additional load/write and needs its own authorization and resource bound. |

Promotion requires a destination that supports global scope and the relevant access controls. Specify source `caslib` and `name`, and destination `targetLib` and `target` explicitly. The observed `table.promote` defaults `drop=True` (drop the session-scope source after promotion); select this deliberately. It has no generic `replace` parameter. Do not pre-drop a global table to work around a name collision, and keep `quiet=False` so a missing source is not disguised as an OK response.

For an explicitly authorized publication, using the helper below:

```python
checked(conn, "table.promote", caslib=scratch_caslib, name=scratch_name,
        targetLib=published_caslib, target=published_name,
        drop=True, quiet=False)
published = checked(conn, "table.tableExists",
                    caslib=published_caslib, name=published_name)
if published["exists"] != 2:
    raise RuntimeError("Expected a global in-memory table")
```

This does **not** save a file. If persistence was separately requested, inspect `table.save`, then specify the input table and backing destination independently, for example `checked(conn, "table.save", table={"caslib": input_caslib, "name": input_name}, caslib=output_caslib, name=output_file, replace=False)`. Supported file types and connector behavior are deployment/release dependent; a suffix alone is not a support guarantee.

`table.dropTable` removes an in-memory table; `table.deleteSource` removes backing data. Neither is an automatic substitute for the other. Same-named session/global tables can shadow each other; dropping repeatedly until a name disappears can delete shared data. Never use repeated drops as cleanup. Clean up only known task-owned scratch artifacts, with verified scope and authorization. Ending an owned session removes its session-lifetime resources, but does not undo promotion or delete saved data. `conn.close()` defaults to disconnecting without explicitly ending the session; use `conn.terminate()` for a newly owned session, not for an attached shared session.

## Bounded existing-table inspection

Use when the task authorizes reading an existing in-memory table or verifying an output. This recipe creates no table, promotes nothing, saves nothing, and does not fetch the whole table. It does run CAS actions; use only an approved target/session. The optional count equality is meaningful only when the task supplies an expected count and the input is stable during the checks.

```python
def checked(conn, action, **params):
    result = conn.retrieve(action, **params)
    # Strict policy for this validation recipe: warnings require manual review.
    # Inspect result.status/reason/status_code/messages in a protected context,
    # redact sensitive content, and explain any accepted warning explicitly.
    if result.severity != 0:
        raise RuntimeError(f"{action}: CAS diagnostics require review")
    return result


def inspect_existing(conn, caslib, name, columns, expected_rows=None):
    if not columns:
        raise ValueError("Choose explicit authorized columns before fetching")
    checked(conn, "builtins.help", actionSet="table")
    checked(conn, "builtins.reflect", action="table.fetch")
    exists = checked(conn, "table.tableExists", caslib=caslib, name=name)
    if exists["exists"] not in (1, 2):
        raise RuntimeError("Requested table is not loaded in this session's view")

    table = {"caslib": caslib, "name": name}
    metadata = checked(conn, "table.columnInfo", table=table)["ColumnInfo"]
    actual = {str(col).casefold() for col in metadata["Column"]}
    if not {col.casefold() for col in columns}.issubset(actual):
        raise RuntimeError("Required columns are missing")
    count = checked(conn, "table.recordCount", table=table)["RecordCount"]
    rows = int(count.iloc[0]["N"])
    if expected_rows is not None and rows != expected_rows:
        raise RuntimeError("Row count differs from the task's expected count")

    sample = checked(conn, "table.fetch", table=table,
                     fetchVars=[{"name": col} for col in columns],
                     to=5, maxRows=5, index=False)["Fetch"]
    if not 0 <= len(sample) <= min(5, rows):
        raise RuntimeError("Bounded fetch result is inconsistent with the count")
    return {"scope_code": exists["exists"], "rows": rows,
            "column_metadata": metadata, "sample": sample}
```

Call `inspect_existing` with an already approved connection and explicit caslib/table/column names; do not print the returned sample unless disclosure is permitted. Inspect the returned metadata against expected types, lengths and formats, and use `table.tableInfo` when source/scope details are needed. `recordCount` returns a small result but can require substantial server work. Where a full count is not authorized, omit that part and report the narrower check rather than claiming full-table validation. This recipe's multi-action observations are not an atomic snapshot of a concurrently changing table.

Success here means severity-checked action results, an existing table with known scope, required column presence, a count comparison when supplied, and no more than five returned rows of the selected columns. It does not prove analytics correctness, backing-store durability, cross-session visibility, deterministic row order, or compatibility with another SWAT/platform release.

## Quick-start learning reference

When following SAS Quick Start videos or translating a SAS/CASL demonstration into SWAT, use the [pinned Compute/CAS/CASL example](https://github.com/sassoftware/sas-viya-quick-start/blob/f332021071e4667eedab16e690ab1d8a7ac93c4e/Compute_CAS_CASL.sas) for execution-location concepts, not as a Python API contract. It contains SAS programs and CASL actions, not SWAT calls. Select the actual CAS action through the target's `help`/`reflect` and this skill's result checks; do not assume every Compute procedure runs in CAS or that a local Python path is visible to CAS.

Before adapting the example, inspect its included [data loader](https://github.com/sassoftware/sas-viya-quick-start/blob/f332021071e4667eedab16e690ab1d8a7ac93c4e/_Load_Quick_Start_Data.sas). It writes SAS datasets, drops named `CASUSER` tables (`homeequity`, `customer`, `countrylookup`), promotes demo tables and saves backing files with replacement. The comparison program also uses a hard-coded `c:\mydata` path and replacement operations. These are mutations, not connection/health checks; `quiet` suppresses some diagnostics, not their effects. Inspect existing table/file names and consumers, and obtain authorization for all replacements before any run. A personal caslib is not proof the names are disposable.

For an approved isolated learning exercise, declare the Compute/Python/CAS data locations, use reviewed task-owned destinations and preserve both session ownership and the required output lifetime. Follow [table lifecycle](#table-lifecycle) to verify scope and backing storage separately. If the original video requires unchanged names/code, use an isolated environment rather than overwriting existing work. Consult the matching server/action documentation for implementation details; the tutorial snapshot is not a release support matrix or production fraud-data recipe. See [source limits](../SOURCES.md#quick-start-learning-source).
