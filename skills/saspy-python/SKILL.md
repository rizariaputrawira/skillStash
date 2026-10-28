---
name: saspy-python
description: "Use when running SAS programs from Python through SASPy, configuring SAS sessions, exchanging SAS datasets with pandas, or diagnosing submission and conversion problems."
---

# Python-driven SAS sessions

Use this skill as the coding agent preparing or diagnosing a SASPy workflow. It supplies procedure, not permission to connect, install packages, submit SAS code, transfer sensitive data or modify a server. It works without sibling skills or a workflow suite.

SASPy runs SAS code in a SAS session. Its HTTP access method targets **Viya Compute**, not CAS and not every Viya service. SWAT is the CAS-action interface; model registration/publication and Fraud Decisioning application APIs require their own supported interfaces. Do not substitute an interactive SASPy session for a transaction-time decision/scoring contract.

## 1. Establish target, versions and effects

Record the requested program/data operation, environment, source and destination, permitted reads/writes/transfers, local result limits and session owner. Distinguish the Python filesystem, SAS server filesystem, SAS libraries and CAS caslibs; identical path or table names do not make them interchangeable.

Record independently:

- Installed SASPy distribution version/import location, Python and relevant pandas/Arrow versions; OS, Java/IOM dependencies if selected.
- SAS 9.4 maintenance level and Workspace Server configuration, **or** Viya family/platform release, cadence/updates and Compute context. The SAS engine version is not the Viya platform release.
- Session encoding, relevant SAS product licenses, authentication policy and server capabilities.

Research baseline: **SASPy v5.110.0**, release date **2026-10-20**, commit `ab3c55f0b6366418f5c63ab5c0d81e1c1bca51d3`. This is not a support matrix or required upgrade. Read [SOURCES.md](SOURCES.md) for exact evidence, conversion-change history and licensing boundaries. Use installed signatures and release-matched source when the target differs. Local preparation can proceed without a SAS server; unknown deployment facts block live compatibility claims, not offline work.

## 2. Load the relevant branch

- For session setup, authentication, TLS, HTTP versus IOM/SSH/STDIO, or wrong-target errors, read [connections](references/connections.md) before constructing `saspy.SASsession`.
- For code submission, logs, `PROC PRINTTO`, cancellation, aborts or cleanup, read [submission and lifecycle](references/submission-and-lifecycle.md).
- For pandas/SAS transfers, dataset inspection, timestamps, encodings, Arrow/Parquet or validation/reconciliation, read [data exchange](references/data-exchange.md).

These branches can combine: connection success does not establish code success, and code success does not establish data fidelity.

## 3. Prepare the narrow workflow

Use explicit configuration/profile, library/table names and output identities. Inspect existing code for writes, `%include`, OS commands, macros, external connections, log redirection and termination before submission. A read-oriented Python call may submit SAS code and create WORK intermediates; describe those effects accurately. Keep credentials and raw fraud/customer records out of source, notebook output, rendered HTML and reports.

Review dynamic SAS fragments as executable code: dataset options, WHERE clauses, identifiers, macro values and included paths are not SQL-style bound parameters. Do not interpolate untrusted text directly into SAS programs. Prefer fixed reviewed programs and a defined input schema with appropriate SAS quoting/validation for each value type.

Use bounded server-side filtering/projection/aggregation before transfer. Choose output libraries and replacement semantics explicitly; never rely on an omitted libref or helper's default output name for shared data. Run only authorized stages; avoid a write just to make a smoke test look successful.

## 4. Execute and verify at the requested layer

For authorized execution, establish an owned session or follow the existing session owner's policy, run a non-sensitive identity probe, then execute the smallest operation that proves the requested behavior. Inspect exceptions **and SAS logs**; a returned dictionary, SASdata object or HTTP success is not proof of successful SAS processing. Capture submission-specific logs before later helper calls replace the last-log view.

Verify output identity/existence, schema and required counts/aggregates, then compare conversion boundaries against approved expected values/tolerances. For a transport failure or timeout after a write, inspect target state before resubmitting; do not assume cancellation rolled back SAS side effects. End only sessions owned by this task, after required WORK outputs have been consumed or explicitly persisted.

If no authorized SAS target is available, finish source-checked code/configuration and local syntax/schema checks; name the missing live evidence. Do not fabricate authentication, SAS execution, conversion correctness or performance results.

## Result contract

Return: requested operation and permitted effects; SASPy/Python/dependency and SAS/Viya version evidence; selected connection/context/profile without secrets; execution/data location; program or files prepared/changed; sanitized SAS diagnostics; output identity/schema/count/reconciliation evidence; transfer bounds and actual transfer; session cleanup; source URLs and unverified prerequisites.
