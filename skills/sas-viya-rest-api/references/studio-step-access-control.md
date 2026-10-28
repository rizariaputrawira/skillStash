# Studio step access control reference

Load for an explicitly assigned review or change to SAS Studio / SAS Data and AI Studio step access. This is an administration reference, not a ready-to-run installer or approval to change identities or authorization rules.

## Scope and source baseline

The [SAS step-access-control project](https://github.com/sassoftware/sas-data-and-ai-studio-step-access-control/tree/111f85281e48f72c1a4ae62c5e6f8b8451a0cf10), commit **`111f85281e48f72c1a4ae62c5e6f8b8451a0cf10`**, creates custom groups and `prohibit` / `read` rules on step resources. It is useful for aligning Studio step availability with an organization's approved access policy. It does not establish protection for CAS data, Compute submission, underlying decision services or alternate clients, and it is not a contract-entitlement audit. A group labeled NoDecisioning is not proof of Fraud Decisioning product authorization coverage.

The README requires administrative capability and warns about non-public APIs. Creation scripts use `/dataFlows/steps`, `/dataFlows/stepCategories` and a group-creation operation the source labels undocumented. Discovering one route in source does not establish a supported target contract. This reference does not override the core skill's documented-exchange requirements; if non-public API use is explicitly requested, require the deployment owner's acceptance of its support/change risk and independently verify the target operation before implementation. Otherwise deliver a review and documented/manual alternative where available.

## Inspect before reuse

| Observed at the pinned source | Required treatment |
| --- | --- |
| Group creation uses `SASSDAIStudio` and `SASDAIStudioAdavanced`; rule creation uses `SASDAIStudio` and `SASDAIStudioAdvanced`. Cleanup still refers to Basic/Analyst/Engineer groups. | Build one reviewed mapping of actual group IDs to the proposed policy; do not run the scripts sequentially assuming their names agree. |
| Creation joins step inventory to CSV on display name and excludes rows with missing mapped tier values. | Compare the full inventory with the policy; surface unmatched/new/ambiguous names for owner review rather than silently omitting them. Do not assume display names are stable across releases or locales. |
| Inventory requests use `limit=10000`, cleanup uses `limit=1000`, without a paging loop in the inspected code. | Verify collection semantics and completeness; follow target-supported pagination rather than treating a large limit as proof of a complete inventory. |
| Rule creation directly posts rules without reconciling existing rules in the inspected procedure. | Plan a reviewed delta against current state. Capture results and reconcile an uncertain outcome before retrying; rerunning an upgrade script is not proof of idempotency. |
| Cleanup filters only by principal and deletes the returned rule IDs; it does not narrow to step URIs or rules created by this operation. | Do not use that cleanup against shared groups. Plan rollback from recorded, verified IDs for rules actually created by the authorized change; leave pre-existing rules untouched. |

See [source record](sources.md#studio-step-access-control-source) for exact files and license coverage. This is selected-source inspection, not a full security audit or an observed target failure.

## Release and coverage checks

The upstream changelog records product/tier/CSV naming changes in **2026.06** without a migration script. Its table, README and scripts are not a single consistent mapping. The README explicitly excludes access control for the **Loop** step introduced in **Stable 2026.08**; the changelog also records steps added or removed in later releases. Record target release/cadence/patches, installed step IDs and policy-file revision separately; do not apply the snapshot to an older or newer installation by name alone.

## Authorized change recipe

1. Establish the target and permitted effects through the core skill. Inventory relevant steps, existing groups/memberships and applicable rules through authorized interfaces; store sanitized evidence and separate inaccessible results from an empty inventory.
2. Have the owner approve the identity-to-step policy, including mixed/overlapping group membership and unmatched or unsupported steps. Validate actual authorization precedence using target-matched documentation; do not infer effective permission from a single rule or a group name.
3. Prepare a dry-run delta listing exact principal IDs, step URIs, intended rule semantics, existing versus new resources, known exclusions and rollback criteria. Retain only protected evidence references, not tokens or personal membership exports in reports. Do not change SAS entitlements or broad permissions to make an example work.
4. When mutation is authorized, apply the reviewed delta with checked HTTP outcomes and resource IDs. Preserve existing resources and record created/changed IDs and prior values as applicable. Reconcile partial failures before proceeding or retrying. Group deletion requires separate ownership/membership checks and explicit scope; it is not a generic rollback shortcut.
5. Verify effective permitted and prohibited behavior with approved representative identities, overlapping memberships and exact target steps. Do not impersonate a user without authorization. UI visibility alone is not proof that alternate execution paths are denied. Record unsupported coverage and avoid a blanket security/compliance claim.
6. After an authorized platform update, compare inventory and policy changes before proposing new rules. A documented rollback removes only verified resources owned by this change, with current-state checks and evidence that unrelated rules remain intact.

Without an authorized target, deliver the mapping/delta recipe, known hazards and validation plan. No upstream script, live authorization mutation or identity creation was executed for this reference.
