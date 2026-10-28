# Lock, publish and verify the runtime decision

Use the [pinned publication implementation](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/src/sas_mcp_server/tools/decisioning.py) and [MAS scoring tools](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/src/sas_mcp_server/tools/model_scoring.py), then the discovered deployment schemas. Authoring, locking, publication, lookup activation and scoring are separate effects.

## Freeze the intended state

Review the working decision/rule set and its referenced revisions first. When locking is authorized, inspect the tool response carefully: baseline locking freezes the previous current state and opens a new working revision. Dedicated rule-set/flow tools return `locked_revision` alongside working-state fields; the shared lock tool also separates the revisions. Capture the frozen ID explicitly and read it back. Do not publish a top-level ID assuming it names the locked revision.

Check dependency pins, generated code and signature against the approved change. Newer working edits do not automatically update an existing locked dependency. Conversely, an active lookup version can change behavior outside the locked decision's code; record its identity/activation state with the release evidence.

## Publish to the actual destination

For a supported MAS target, baseline `publish_decision_flow` takes decision ID, **locked revision ID**, publish name and destination name; confirm the destination identity/type rather than relying on default `maslocal`. Review existing published-name/module consumers and replacement implications before sending. Another destination/runtime needs its own verified interface; do not pretend this tool covers all Fraud Decisioning deployment paths.

The baseline generates code for MAS and preserves plain decision input names in its wrapper. This differs from earlier versions' code target and from UI-published modules with trailing underscores. The release notes also state `PathID` can be empty after the v1.19.0 MAS publication path; a path-trace acceptance requirement therefore needs explicit verification or a supported alternative, not a silent waiver.

Publication is asynchronous. The tool's poll deadline does not necessarily cancel publication. Inspect the returned publish record and actual `moduleState`:

| Observed result | Required action |
| --- | --- |
| `completed` with a nonempty `moduleId` and no unresolved module errors | Read the module step signature at that exact ID; then perform separately authorized business tests |
| `pending` | Retain publish record/job identity; reconcile through a supported read interface or administrator evidence before retrying; do not re-publish merely because the tool stopped waiting |
| `failed`, `error`, `canceled` or `cancelled`, or reported `moduleErrors` | Inspect sanitized failure details and any partial state; block dependent scoring/promotion |
| Missing/unrecognized state or module identity | Treat outcome as unknown; do not derive module ID from the publish name or claim success |

At baseline the MAS module ID is server-generated and **not** the publish name, decision ID or revision ID. A successful MCP call can return pending/failed publication data. Save these identities separately in the caller's existing release record. Do not invent a generic MCP publication-status tool; discover an actual read path, using separately authorized REST only where permitted and contract-verified.

## Inspect the scoring contract

When mapping tabular/file inputs, validating data grids or reviewing batch/regression scoring, read [MAS input and batch validation](mas-scorer-validation.md).

Use `get_mas_module_step_signature` for the exact returned module and intended step. Verify required input names/types/order, output schema and execution semantics. Baseline `score_data` can map plain names to UI-published trailing-underscore names after a 400 and reports that as `inputs_renamed`; inspect the map, but prefer the actual signature rather than relying on fallback. Check blank/missing values and typed identifiers explicitly.

Scoring invokes code and is withheld in baseline strict read-only mode. Review external calls, persistence or other side effects of the decision/code/model before using even a synthetic test. Do not send real transactions, account data or credentials as a connectivity probe. Code execution and expression compilation are also effects; do not route around a filtered scoring tool through Compute.

## Business-result verification

Derive expected outcomes from the approved specification, not from the implementation being tested. Compare exact discrete actions/reason codes and agreed numeric tolerances; inspect SAS/MAS errors separately from transport success. Cover the applicable boundaries:

- Equality at each threshold and just below/above it, using the actual numeric precision/domain.
- Overlaps, gaps, first-match/priority order and default/unmatched paths.
- Missing/blank values, unknown lookup keys, active versus edited lookup versions and malformed types.
- Long/leading-zero identifiers, string/numeric distinctions, amount precision and date/time edge cases.
- Dependency/model outputs, sub-decisions, declared output variables and required audit/path evidence.
- An unchanged-control case to detect unintended behavior changes and approved regression cases for the modification.

The following JSON is an **original synthetic test-spec illustration**, not a tool request, real fraud threshold or actual scoring result. It describes a toy rule in which a non-missing integer `units >= 10` produces `REVIEW`, otherwise `PASS`; missing input must be rejected. Replace this policy with the actual approved requirement before real testing.

```json
{
  "synthetic": true,
  "cases": [
    {"input": {"units": 9}, "expected": "PASS"},
    {"input": {"units": 10}, "expected": "REVIEW"},
    {"input": {"units": 11}, "expected": "REVIEW"},
    {"input": {"units": null}, "expected": "INPUT_REJECTED"}
  ]
}
```

A syntactically valid test specification is not an exercised decision test. Record actual input-case IDs, module/step/revision/lookup evidence, returned outputs, comparison results and sanitized diagnostics when live execution is authorized. Unknown or partial results block an end-to-end success claim. Broader Fraud Decisioning transaction ingestion, alerts/case management and operational integration require their own product-specific acceptance checks; this MCP decisioning slice does not establish them.
