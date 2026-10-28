# Decisioning assets, rules and lookup versions

Baseline owners: [decisioning tools](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/src/sas_mcp_server/tools/decisioning.py), [shared lifecycle](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/src/sas_mcp_server/tools/decisioning_lifecycle.py), and [lookup tools](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/src/sas_mcp_server/tools/decisioning_lookups.py). Discover the installed schemas first; this reference is not a universal request-body specification.

When reviewing supplied Studio data-grid serialization or lookup-import work, read [Studio decision-data preparation](studio-data-grids-and-lookups.md) for the offline preparation branch; activation policy remains here.

## Read and specify before writing

Record asset type/ID/folder, full signature, current working and approved locked revisions, rule/step ordering, all referenced assets and lookup tables, and downstream runtime consumers. Read rules as well as their rule set; do not infer behavior from a name or signature. Baseline shared reads can summarise content above 150,000 characters, and list helpers have result limits. A summary is insufficient evidence for a full-content edit/review.

Keep each business term's type, direction, domain and default explicit. Check missing values, string-versus-numeric identifiers, decimal precision, date/time policy, condition order and stop/continue behavior against the actual business requirement and target product contract. Never invent real fraud thresholds or operational allow/deny policy. Confirm existing dependencies and resource versions rather than taking a newest selector as approval.

Creating an asset needs an approved SAS Content folder. The baseline accepts path/ID/URI or a configured default; an unspecified default is not permission to store in an arbitrary folder. Inspect the returned folder after each write. For rule sets/flows, updates do not move an already-filed asset; they can file an older unfiled asset. Moving/reorganizing content and changing permissions are separate operations, not implied by a new folder argument.

## Rules and flow wiring

1. Prepare the smallest reviewed rule/signature delta. Use the discovered rule tool contract rather than inventing DS2/JSON structures. A schema-valid request can still save invalid conditions/actions: inspect returned `problems` and re-read stored rules. Compiler warnings, including undeclared variables, require interpretation rather than acceptance based on `valid=true` alone.
2. When compilation is authorized, `validate_decision_expression` checks an expression against a declared signature; it is server work. This does not prove rule execution order, complete flow compatibility or analytical correctness. For code files/custom functions/trees, use only the advanced tools actually exposed and inspect generated code/validation results appropriate to the asset type.
3. Select exact dependency revisions and mappings before writing a flow. In baseline `steps`, omitted revision selection uses the newest locked revision and can **lock a referenced asset if none exists**; `revision="working"` explicitly selects editable state. Do not silently trigger locking or rely on mutable working state for an approved release. Use a supported explicit selector from the installed contract and confirm the actual selection in `steps_report`. If the helper cannot represent the approved pin, stop that write instead of substituting latest.
4. Review term mappings, type families and every automatically added variable. Baseline `add_missing_variables` defaults true, model outputs can become decision variables, and mappings default by name. These conveniences can alter the effective input/output contract; inspect `steps_report.variables_added`, stored signature and step inputs before approval.
5. Assignments set **constants**, not evaluated expressions: a string such as `amount * rate` is not a calculation. Put approved calculations in a supported rule/code step. Range branches compare inclusive ends; shared endpoints are refused, while using approximate decimal endpoints can leave gaps. Derive boundaries from the domain/precision contract or use a suitable condition structure, then test equality and both adjacent sides. Do not copy illustrative ranges as production fraud policy.
6. Re-read the stored flow, selected revision dependencies, folder, variable mapping and generated code where relevant. Compare with the reviewed delta before any locking/publication. Saving alone is not a successful end-to-end implementation.

## Lookup edits versus activation

Lookup table ID, editable version and active version are distinct. Read exact version IDs/labels, active state, paged entries and `used_by` consumers. A developing version is not what deployed decisions necessarily use. The baseline defaults `get_lookup_table(version="current")` to active if available; `newest` can be a different version. Bind every review/edit/test to its intended version explicitly.

`update_lookup_table` can add/overwrite entries, remove keys or replace the entire entry set. Review the exact operation and complete current entries before replacement. Edits to an active table start a new version; inspect the returned version and change set. Keep `activate=false` for an edit-only assignment. An `activate=true` option combines authoring with runtime publication and requires both effects to be authorized.

**Activation is a production-impacting change even without decision republishing.** The release describes lookup activation publishing to MAS/CAS and already-published decisions using newly active entries on later calls. Establish every affected consumer/environment and approval before activation; it is not scoped merely by the caller's test decision. Prefer an isolated lookup/test environment for approved experiments rather than activating shared fraud reference data.

For an authorized activation, specify the reviewed version rather than blindly accepting default `newest`; inspect `outcome`, activation errors and active version using a fresh read. `active`, `running` and `failed` mean different things: exceeding `wait_seconds` can return `running`, not a rollback. `republish` copies active content again; an older/failed version activated for rollback is cloned into a new version at baseline. Record those effects and verify runtime behavior rather than repeatedly activating after an uncertain response.

Before deleting any asset, inspect references and deployment consumers; a tool's deletion guard is not a complete dependency audit. No deletion is authorized merely because an earlier create/update failed.
