# Runtime and version binding

## Applies-to and evidence boundary

This procedure is version-neutral; the claims below are bound to the consulted editions. Owning sources: [CAS DATA Step Programming](https://documentation.sas.com/api/collections/pgmsascdc/v_056/docsets/casdspgm/content/casdspgm.pdf?locale=en), title applicability **2024.04***, displayed publication **June 15, 2026**, especially printed pp. 4–10 and 36–41; and [Language Reference: Concepts, Sixth Edition](https://documentation.sas.com/api/docsets/lrcon/9.4/content/lrcon.pdf), **SAS 9.4**, displayed **April 5, 2023**, chapters 2, 4 and 19. Consulted **2026-10-28**; full records: [sources](../SOURCES.md).

The CAS title's asterisk says additional versions might apply; it does not identify those versions. Collection `v_056`, publication date and deployment release are different facts. The SAS 9.4 concepts edition is a language source, not proof that every maintenance level or Viya runtime implements every described feature. No release range, latest-release support or DS2 equivalence is established here.

## Bind only the axes that affect this task

1. Record the product and actual execution environment: SAS 9 DATA step/PROC, Viya Compute, CAS DATA step, DS2 or a database receiving pushed-down SQL. A file named `.sas` or a CAS-resident output does not identify where code executes.
2. Obtain the relevant target identity from existing environment inventory, logs or authorized metadata inspection. For SAS 9, maintenance/hot-fix details matter when a feature or defect depends on them. For Viya, record release and cadence when comparing release-specific support. Record OS/encoding, input/output library engines, product availability and options only where they affect the task. Keep client/UI/library version separately; it is not the server runtime version.
3. Find target-matched official language and product documentation for each consequential construct. Capture the exact URL, document edition/version label, consulted date, supported context and relevant restrictions. Syntax describes a construct; compatibility requires matching product, runtime, engine and feature availability.
4. Assign a status per claim: **documented for matched target**, **unknown/not established**, or **unsupported by explicit target documentation**. A mismatch means inspect target-matched docs, not extrapolate forwards/backwards. If target identity or docs are unavailable, prepare a clearly labeled offline draft, expected outputs and a specific documentation/runtime check. Do not claim executable compatibility or label the entire feature unsupported merely because evidence is missing.
5. A matching edition remains documentation evidence only. For authorized execution, separately capture actual runtime/log evidence and reconcile outputs. Permissions, configuration and data-dependent behavior are not proven by documentation.

## Conditional CAS adaptation

Load this branch only if CAS execution or movement to/from CAS is material. Reuse the already authorized session and table-management owner; do not create sessions, change authentication, promote or drop tables from this guidance.

1. Establish whether the task requires CAS execution or merely CAS storage. In the consulted 2024.04* manual, a Compute DATA step can process data and then load a CAS table. Thus a CAS engine output alone is not CAS-execution evidence.
2. Inspect input/output engines and supported language elements against the **target release**. The consulted manual requires CAS engine input/output references associated with the same session for CAS DATA step processing. It documents restrictions, not interchangeable SAS 9/Compute/CAS execution.
3. Treat these as **evidenced runtime deltas in that edition**, not a release-change history:
   - `DATALINES`, `INFILE` and `INPUT` statements are not supported for an executing CAS DATA step; prepare raw-data input on Compute when authorized, rather than transplanting a raw-input fixture to CAS.
   - Macro code executes in the SAS client session on Compute; generated DATA step code may execute in CAS. Macro-related DATA step interfaces, including `CALL SYMPUTX`, are listed as unsupported in CAS.
   - Unsupported elements can cause Compute execution followed by transfer to CAS, rather than necessarily stopping with an error. The documented log marker is `Running DATA step in Cloud Analytic Services`; capture it when requiring CAS execution and investigate its absence. Confirm target documentation if diagnostic behavior differs.
   - BY grouping/order and multithreaded execution differ; CHAR/VARCHAR and encoding can change length semantics. Never carry a sequential retained-state or observation-order assumption into CAS without target-specific reasoning and checks.
4. Decide whether preserving Compute execution, rewriting for supported CAS semantics, or declining the migration satisfies the request. State data-movement, resource and permission consequences before any authorized action. A fallback can violate the requirement even when values look correct.
5. Compare schema, key groups, counts and aggregates independently of physical row order. If order is part of the requested result, define a complete ordering key and verify through a target-supported method; do not equate table display order with a distributed execution guarantee.

No version-to-version change chronology was established by this research. DS2, FedSQL, database pushdown and specialized procedures require their own target-matched documentation before use; this reference supplies no runnable substitutes for them.
