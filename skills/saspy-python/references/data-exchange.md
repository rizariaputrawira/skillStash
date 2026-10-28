# SAS datasets, pandas and conversion fidelity

Baseline: [v5.110.0 session APIs](https://github.com/sassoftware/saspy/blob/v5.110.0/saspy/sasbase.py), [SASdata APIs](https://github.com/sassoftware/saspy/blob/v5.110.0/saspy/sasdata.py), and [release changes](https://github.com/sassoftware/saspy/blob/v5.110.0/CHANGELOG.md). Signatures, transfer-method kwargs and conversion behavior must match the installed access method/version.

## Choose location and direction

| Operation | Meaning and verification |
| --- | --- |
| `session.sasdata(table, libref=...)` | A server dataset reference, not a pandas DataFrame or proof of successful processing; verify explicit library/table access |
| `session.sasdata2dataframe(...)` / SASdata `.to_df()` | Transfer SAS rows into Python memory; filter/project/bound first and inspect conversion results |
| `session.dataframe2sasdata(...)` | Write a local DataFrame into an explicitly selected SAS library/table; check destination/replacement permissions and verify actual output |
| SAS `%include` / libname / server-side program paths | Resolve in the SAS runtime, not automatically on the Python host |
| Arrow/Parquet export | A separate conversion/output contract; confirm installed dependencies, path location, schema, options and release-specific fixes |

Do not label `read_csv` or an upload/download helper client-local or server-local from its name alone. Verify the selected method's documentation and implementation for the access method. A SAS library can use different engines; a CAS-backed libref does not make the SASPy connection a SWAT session or prove CAS table persistence.

## Bounded existing-table inspection

For an already authorized session, read only approved columns and bound the export on the SAS side. This original example uses the public sample table `SASHELP.CLASS`; the table's availability still needs checking on the real server. It is not a template for exporting fraud records without permission.

```python
# Requires an existing authorized session named session.
sample = session.sasdata2dataframe(
    table="CLASS",
    libref="SASHELP",
    dsopts={"keep": ["Age", "Height"], "obs": 5},
)
```

Inspect the submission/transfer diagnostics and verify the returned schema and upper row bound without printing the raw DataFrame. `obs` is a SAS dataset option, not a universal SQL LIMIT after every filter; do not assume it means the first N matching rows for all views/engines/options. A sample does not establish completeness, uniqueness or analytic correctness. A transfer bound does not bound all server scan/aggregation costs.

For production reconciliation, select required metadata and server-side counts/aggregates with reviewed SAS code, then export only necessary results. Establish snapshot/isolation expectations if data can change between inspection and transfer; separate queries are not automatically an atomic snapshot.

## DataFrame to SAS

Before calling `dataframe2sasdata`, check explicit libref assignment, table identity, existing output, replacement policy and authorized lifecycle. At the baseline, an invalid libref can log an error and return `None`; an object return is not proof of successful import. Do not rely on default `_df`/WORK/USER destinations. Stage to a task-owned name only when writes are authorized; publish to shared destinations only after required checks and with an approved replacement strategy.

The DataFrame index is **not transferred as a column**. If it is a business key, deliberately materialize it and check naming/conflicts. Configure character lengths in SAS **bytes**, not just Unicode character counts; review `char_lengths`, `datetimes`, `outfmts` and encoding behavior for the installed version. Fail on encoding loss rather than silently choosing replacement/ignore behavior. SAS format metadata alone is not a change to underlying stored values.

## Required fidelity checks

Choose approved synthetic edge cases or existing authorized expected results; do not send real fraud/customer data as a setup test.

- **Identifiers:** leading zeros, long numeric-looking strings, blank values, duplicate keys, Unicode and punctuation. Do not coerce account/card/transaction identifiers to floating point; inspect source representation and precision loss before conversion.
- **Numeric values:** agreed precision/scale and tolerances for amounts/scores; missing values versus zero; SAS special missing-value semantics where material. Do not promise pandas preserves every SAS missing category.
- **Dates/times:** distinguish SAS date, time and datetime meanings, display formats, timezone policy and pandas range limits. At baseline, pandas out-of-range values can become `NaT`; optional `tsmin`/`tsmax` replacements are explicit semantic changes, not harmless fixes. Reject or report unexpected nulls rather than hiding them.
- **Text:** encoded byte length, truncation, trailing/leading blanks, empty/missing distinctions, embedded separators/newlines and round-trip fidelity. Some transfer modes replace control/separator characters; inspect the relevant options before claiming a lossless round trip.
- **Shape and metadata:** row counts, columns/types, index handling, labels/formats where required, empty datasets and required aggregates. Verify both the SAS output and returned Python values against the task contract.

The tagged changelog records 5.109.x fixes for HTTP date detection and Arrow/Parquet conversion, including previously silent null conversions. Do not infer behavior from `main` documentation or assume identical pandas, Arrow and Parquet schemas. Record dependency versions and exact export options. No performance claim is established by a method name or an upstream historical speed comparison.
