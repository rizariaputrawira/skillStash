# Submission, diagnostics and lifecycle

Baseline contracts: [v5.110.0 SASsession source](https://github.com/sassoftware/saspy/blob/v5.110.0/saspy/sasbase.py) and [tagged limitations](https://github.com/sassoftware/saspy/blob/v5.110.0/saspy/doc/source/limitations.rst). Read [connections](connections.md) before opening a session.

## Minimal authorized session probe

The following original example requires a trusted installed package, an existing approved config/profile, and permission to start a session. `cfgfile` is a local Python configuration file; it must be supplied as a real approved path. The fixed SAS statement writes engine-version evidence to the log; it does not identify the Viya platform release or validate data access. The function returns the log for controlled inspection instead of printing it.

```python
import saspy


def probe_owned_session(cfgfile, cfgname):
    session = saspy.SASsession(cfgfile=cfgfile, cfgname=cfgname)
    try:
        result = session.submit(
            "%put SKILLSTASH_ENGINE_VERSION=&sysvlong4;", results="TEXT"
        )
        return result["LOG"]
    finally:
        session.endsas()
```

No live result is supplied here. Check for the expected marker and coherent engine version, inspect any SAS errors/warnings, and verify target/context from approved deployment evidence independently. Configure plain logs (`colorLOG=False`) when automation requires text; `results="TEXT"` controls listing format, not colored log rendering.

## Submission completion

`session.submit(code, results="TEXT")` returns `LOG` and `LST`. `LST` can be empty for valid non-listing code; nonempty HTML is not analytical success. `submitLOG` prints the log, while `submitLST` renders results; avoid those convenience methods when sensitive diagnostics need controlled handling.

1. Review the whole submitted program's effects before execution, including autoexec, included programs and macros. Identify expected output tables, files and completion signals. Do not submit arbitrary legacy batch code unchanged into an interactive session.
2. Save `result["LOG"]` securely for that submission and inspect errors, warnings, uninitialized variables, invalid-data/conversion notes and unintended row counts. SAS errors do not necessarily become Python exceptions. A simple search for `ERROR:` is only triage: quoted source text/localized messages and semantic failures need contextual inspection.
3. When needed, capture SAS status at the relevant step inside the reviewed program and define a task-specific completion signal. Later procedure calls can change status; a final `SYSERR` or marker alone does not prove earlier steps succeeded. Do not depend on a marker after code that can terminate/cancel before reaching it.
4. Verify expected outputs independently: explicit libref/table/path, existence, schema, counts and task-specific reconciliation. Preserve a diagnostic distinction between SAS processing failure, missing listing, transfer failure and cleanup failure.
5. Redact diagnostics before sharing; program source, macro expansion, libname statements and logs may reveal credentials or fraud/customer data. Do not enable broad debug logging to investigate authentication.

`lastlog()` can refer to the latest internally submitted helper code, not the program you care about. `saslog()` covers the wider session and may include unrelated sensitive content. Use the captured submission result rather than substituting a later last-log view.

## Session-disrupting code

- **`PROC PRINTTO`:** redirecting LOG deprives SASPy of information it uses for synchronization/metadata parsing and can hang or break calls. Prefer a reviewed program that leaves LOG available. Where redirection is necessary, upstream documents `submit(..., printto=True)` to append the reset, or an explicit `proc printto; run;`. Neither proves recovery when abort/termination prevents the reset from running; redirected diagnostics still require separate controlled inspection.
- **`ENDSAS` / terminating `ABORT`:** can end the process before results return. Do not use them inside reusable SASPy programs; let the session owner close it.
- **`ABORT CANCEL`:** upstream distinguishes IOM/HTTP submission boundaries from STDIO, where cancellation can leave the session unable to execute further code. Do not assume the same recovery works across access methods.
- **Long HTTP submissions:** the tagged `submit` contract documents polling options and `submit_timeout`, distinct from the connection socket `timeout`. Verify installed support before using them. A deadline requests cancellation; it is not a transactional rollback or proof that writes did not occur. Reconcile output/job state before retrying a mutation.

## Cleanup and ownership

Keep the session alive while its SASdata references and WORK outputs are needed. `session.endsas()` terminates the SAS process; WORK is session-scoped. Persist outputs only to an explicitly authorized destination before ending the session. A detached Python object is not durable SAS data.

For newly owned sessions, use `try/finally` as above and report cleanup failures alongside the primary error. For caller-owned/shared sessions, do not call `endsas()`; follow the owner's policy and restore only state changed by this task. Do not reset session options, clear all libraries or delete WORK wholesale to recover from one failed program.
