---
name: sas-viya-python-cas
description: "Use when connecting Python to SAS CAS with SWAT, discovering CAS actions, managing CAS tables, or diagnosing CAS data-transfer and session-lifecycle problems."
---

# Python SWAT and CAS

Use this skill to implement or diagnose Python workflows against SAS Cloud Analytic Services (CAS). It provides guidance, not permission to connect, upload, promote, save, overwrite, or delete data. Stay within the assignment's target and authorization.

## 1. Identify the execution target and evidence

Record these axes separately before choosing APIs:

- **Platform:** SAS Viya 3.5, or modern SAS Viya platform release (`YYYY.MM`), Stable/Long-Term Support cadence, and applied update information available from the deployment owner.
- **Server:** target CAS service and server build returned by `builtins.serverStatus`; this is not the platform release identifier.
- **Client:** installed `swat`, Python, pandas, OS/architecture, binary/TK artifact if relevant, and selected protocol. Read installed metadata or manifests; do not install or upgrade merely to inspect.
- **Task:** existing input caslib/table or source, required columns/filter/result, maximum local transfer, output names and lifetime, permitted mutations, and session ownership.

SWAT is a CAS client, not a SAS Compute session client. CAS actions run in CAS; they do not submit arbitrary SAS programs to Compute or expose Compute WORK tables automatically. A notebook running inside SAS Studio does not make its Python filesystem, Compute filesystem, and CAS data sources interchangeable. Route Compute submission requirements to the appropriate Compute interface rather than disguising them as CAS actions.

**Documentation baseline:** independently researched on 2026-10-28 against SWAT **1.18.1** documentation, whose index is dated 2026-06-30, and explicit SAS action-documentation editions. This is not a claim of newest available software, installed support, or live validation. When selecting versions or citing evidence, read [SOURCES.md](SOURCES.md). Viya 3.5 and modern release/cadence identifiers are not interchangeable.

When following the Quick Start videos or translating their Compute/CAS/CASL examples to Python, read the [quick-start learning reference](references/cas-workflows.md#quick-start-learning-reference) before running or adapting the supplied setup programs.

## 2. Establish a safe connection

Obtain the deployment's actual CAS endpoint, exposed port or HTTPS proxy path, approved authentication route, and trust configuration. A Viya web URL alone does not establish a CAS endpoint.

- **Binary:** `swat.CAS(host, port, protocol="cas", ...)`; port must speak CAS binary. The selected client artifact needs the supported native/TK dependencies. Source-only REST installations do not establish binary capability.
- **REST:** `swat.CAS(approved_https_cas_url, ...)`; preserve the deployment's required base path. Do not assume the documentation's example proxy path exists at another site.
- **OAuth:** supply the approved access token through `password=token`, with no username. Resolve conflicting ambient username/authinfo settings first. Tokens are secrets, not ordinary logged parameters.
- **Other routes:** an approved `authinfo=` file must be protected (owner-only permissions on POSIX). Kerberos is binary-only. For these routes, PKCE, or connection failures, read [references/cas-workflows.md](references/cas-workflows.md#connection-and-authentication-branches).

Preserve TLS certificate and hostname validation. Use the approved CA chain, not a verification bypass; `CAS_CLIENT_SSL_CA_LIST` names a **client-side** PEM path for the documented Linux/macOS and Windows REST cases. Windows binary uses the Windows Certificate Store. Do not copy obsolete OpenSSL library filenames from old troubleshooting examples into a modern system.

Finish with a severity-checked `conn.retrieve("builtins.serverStatus")`, retaining only needed, sanitized server-build evidence. A successful login proves neither data authorization nor action availability.

## 3. Discover the target actions, then execute

Use the live server's metadata when live access is authorized:

```python
available = conn.retrieve("builtins.help", actionSet="table")
signature = conn.retrieve("builtins.reflect", action="table.fetch")
```

Apply the result checks below to discovery calls too. `builtins.help` lists actions or shows action help; `builtins.reflect` exposes nested parameter metadata. For a required unloaded analytic set, check the deployment's license/availability, then use `builtins.loadActionSet` with `actionSet=` within the authorized session and inspect its result. Loading a set is not installing a package, enabling a license, or permission to change server configuration.

Prefer fully qualified names such as `conn.retrieve("simple.summary", table=..., inputs=[...])` over ambiguous short action names. Verify action-specific inputs, `casOut`, result keys, and release-specific options from reflection plus the target edition of the official action reference. A pandas-looking SWAT method is not proof that any pandas API or argument is supported.

For **every action**, check both Python exceptions and the returned `CASResults`:

- `severity == 0`: no reported problems; still verify the requested outcome.
- `severity == 1`: warnings; inspect `messages`, `status`, `reason`, and `status_code`, and explain their effect before accepting the result.
- `severity >= 2`: failure; do not consume the result as successful or continue dependent mutations.
- A missing/unrecognized severity or missing expected result key is not success. An action can fail without raising a Python exception. Prefer dictionary access (`result["ColumnInfo"]`) to result attribute access, which can collide with Python methods.

Retain sanitized diagnostics; never dump tokens, connection objects, unrestricted table samples, or credential-bearing action parameters. A transport interruption after a mutation leaves its outcome uncertain: inspect table/file state before resubmitting; do not blindly retry a write.

## 4. Choose data location and lifetime explicitly

A `CASTable` is a client-side reference/view, not a local DataFrame or evidence that a table exists. Keep its connection alive. `SASDataFrame` and pandas DataFrame data live in client memory.

Before reading/loading data or changing table scope, read [references/cas-workflows.md](references/cas-workflows.md#data-location-and-transfer) for the transfer matrix and [table lifecycle](references/cas-workflows.md#table-lifecycle). Declare:

1. Which process can see the source path and which caslib owns the table.
2. Which operations run in CAS, and the bound on returned rows, columns, and sensitive data.
3. Whether the output is session-local, promoted/global in memory, persisted in a backing data source, or an explicitly requested combination.
4. Whether replacement and cleanup are authorized; default to no replacement and no shared-state changes.

Promotion is not persistence. A global caslib does not automatically make its tables global. Saving a table is a separate backing-store write. Do not promote or save a scratch table merely to make an example appear durable.

## 5. Validate the actual CAS outcome and close owned resources

For an existing-table inspection or a post-mutation check, use the bounded executable recipe in [references/cas-workflows.md](references/cas-workflows.md#bounded-existing-table-inspection).

- Check `table.tableExists` for the explicit caslib/name: documented values are `0` absent in memory, `1` session scope, `2` global scope.
- Inspect `table.columnInfo`, `table.tableInfo`, and/or `table.recordCount` as needed. Compare names/types/counts and required scope to the task, not just to whether an object was returned.
- Fetch only approved columns with an explicit row cap using `table.fetch`. A row cap bounds transfer, not server scan/sort cost; a sample alone does not prove completeness. Use server-side aggregates or counts for whole-table checks.
- For writes, verify the destination table/scope or backing file separately, as the lifecycle reference describes. If only code was written, say exactly which checks remain unexecuted.
- End a **new session owned by this task** with `conn.terminate()` in `finally` when its work is complete. For an attached/shared session, follow its owner's lifecycle policy; `conn.close()` defaults to `close_session=False`, so disconnecting is not the same as ending the session.

## Handoff

Return the version/protocol axes, execution location, sanitized action diagnostics, output caslib/name/scope/backing destination, counts/schema and bounded checks actually observed, transfer performed, and cleanup status. Distinguish authored code, source-checked APIs, and live results. Without an authorized CAS target, deliver the code and precise deployment prerequisites, and state **not executed against CAS**.
