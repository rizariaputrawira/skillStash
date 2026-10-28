---
name: sas-programming
description: "Write, explain or debug SAS DATA step, PROC SQL and macro programs, including runtime-sensitive adaptation to CAS."
---

# SAS programming

For an agent preparing or changing SAS language code. Deliver a minimal program or patch, its data contract and evidence-based reconciliation; do not treat a clean log as proof of correct results.

Selection grants no install, authentication, connection, execution, write, overwrite, promotion or deployment authority. Code-edit permission is not permission to submit code; a connected client is not permission to change data. Session/submission clients, CAS actions and table lifetime, REST jobs, Workbench jobs, registration/publishing and lifecycle governance remain with their execution owners. This package does not implement those procedures.

## Workflow

1. **Bind the request and authority.** Identify intended transformation, existing program, permitted code changes, allowed input reads, output destinations and overwrite restrictions. With no execution authority, prepare code and expected checks offline; report live behavior unverified. Never request credentials or reproduce personal data.
2. **Bind the runtime before claiming compatibility.** Read [runtime and version binding](references/runtime-binding.md) whenever selecting executable syntax, interpreting a version claim or considering CAS. Record only relevant axes: SAS product/engine and maintenance level, or Viya release/cadence; distinguish these from client version and actual Compute/CAS/DS2 runtime. Resolve from supplied metadata or authorized inspection. Unknown version permits offline reasoning and drafts, not guessed runnable compatibility.
3. **Bind the data contract.** Record input libraries/tables, filters, observation grain, key multiplicity, missing-key policy, variable types/lengths, formats/informats, encoding, date/time units and required ordering. Define output grain, expected counts, totals and exception policy independently of the implementation. Read [DATA step and SQL semantics](references/data-and-sql.md) for transformations, joins, BY processing, types, dates or missing values.
4. **Make the smallest justified change.** Prefer explicit variables, attributes and step boundaries; avoid adding macro indirection when ordinary SAS code suffices. Keep writes in authorized task-owned scratch outputs until acceptance permits final destinations. For generated code, macro timing, syntax failures or wrong output, read [macro and log debugging](references/macros-and-debugging.md). Do not suppress warnings or coercion notes to make a run look successful.
5. **Reconcile if execution is authorized.** Use the established execution interface, preserving program revision, submitted scope, job/session identity, terminal status, complete relevant log and actual output identities. Inspect errors, warnings and material notes, then compare schema, grain, row/key counts, missingness, duplicates, boundary records and domain totals to independent expectations. For CAS, prove the execution location as well. On uncertain/partial writes, inspect existing outputs before any resubmission; do not retry blindly.
6. **Return a bounded result.** Provide code/patch; input/output contract; target and source applicability; log findings; expected versus observed reconciliation; changed outputs; unresolved facts and exact next check. Distinguish **documented**, **observed on this target**, **unknown/not established** and **explicitly unsupported**. A syntax reference, matching version label or successful submission alone establishes neither compatibility nor business correctness.

## Completion

Offline completion means a source-bound draft plus independently derived expectations and clearly named target checks. Live completion additionally requires terminal execution evidence and reconciled outputs, not merely transport acceptance or an empty error list. Source ownership, editions and rights limits are in [SOURCES.md](SOURCES.md).
