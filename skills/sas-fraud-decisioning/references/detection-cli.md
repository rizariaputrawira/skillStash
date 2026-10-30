# Detection command-line workflows

Load for documented Detection runtime inspection/execution, design-time organization/classification administration, message-schema templates or List Data CLI work. Source: *SAS Detection Command Line Interfaces*, **2025.05**, May 12, 2025, `v_012-P1:frdcli`, printed pp1–56. The manual is older than the package's main 2026.03 baseline. Full extracted text was returned in recorded reads, but this reference is an original command-family synthesis, not a complete verbatim option/example catalog or a claim that every local detail has been incorporated.

## Establish invocation and authority first

Inspect installed version/global help and command-specific help before building a command. The source uses standalone CLI names and examples such as `sas-viya detection`; do not assume aliases, plugins or binaries are interchangeable. A connection profile and authorized sign-in are prerequisites for service operations, not actions performed merely by selecting this skill. Profiles can create `config.json` and token-bearing `credentials.json` under the user's `.sas` directory; never expose those files or copy tokens into reports.

Global controls include output `text/json/fulljson`, profile, endpoint, locale, logging, color, quiet and verbose. Verbose/log-file output can disclose protected details. `--yes-to-all` suppresses confirmation; do not use it to bypass required human approval. Match the actual installed spelling, placement and effect of every option. Preserve operation, target/profile identity without credentials, input file digest, request/job ID and result.

## Select the runtime command

The documented Detection commands accept `--target` for the actual engine URL. Match the installed help before emitting an executable invocation. The reference's existing [conditional command templates](rules-testing-deployment.md#optional-cli-branch--documented-202505-commands) are not runnable defaults.

| Command family | Preparation and result to verify |
| --- | --- |
| `decision_description` | Build, organization, schemas, required input and decision metadata; reconcile against the release candidate. |
| `decision_description_alert_types`, `decision_description_message_classifications` | Actual alert codes/paths and classification IDs/key codes/names. Visibility is not routing acceptance. |
| `decision_description_models`, `decision_description_profiles`, `decision_description_rules`, `decision_description_variable_sets` | Loaded models; profile keys/variables; enabled rule IDs/numbers; variable-set tree. Verify exact revisions/build, not display names alone. |
| `decision_list` | Runtime deployment module; distinguish module presence from correct processing. |
| `health`, `settings` | Health and configuration; redact secrets and validate that the requested target is the intended environment. |
| `stats`, `stats_timeline`, `stats_tools`, `decision_rulemetrics` | Runtime statistics, average timing allocation, tool metadata and UI impact-ratio data. Capture units/windows and raw counts; aggregate averages are not tail latency or business effectiveness. |
| `decision_getverbose`, `decision_setverbose` | Read/change runtime logging. The documented level range is0–13; changes are effects and can expose transaction data. Require approved duration/restoration and observed level. |
| `decision_execute` | Process `--file` through deployed models/rules. This is **not a dry run**. `--offset` and `--limit` bound input; `--schema` and `--messageClassification` override body fields and can invalidate a routing test. Reconcile keyed output, return status, profile state and downstream effects. |
| `decision_score_match` | Compare `--in` messages against `--out` validation results. Supports offset/limit and schema/classification overrides; `--failOnFirst` defaults TRUE. Use the controlled score-match organization and state-reset authority described by the source; never clear live signatures to make a comparison pass. |

Description commands document `--cacert` and paired `--cert`/`--key` TLS options. Their presence is not evidence that every other command accepts them. Use the installed command's transport contract; never disable certificate verification to repair a mismatch. For runtime configuration and errors, read [engine-runtime.md](engine-runtime.md).

## Administer design-time assets

The `detection-definition` family has separate organization, classification, role and capability operations. Bind namespace and exact IDs before a write:

1. Inspect `orgs list/show` with explicit paging/filter/sort. Add approved schema and role associations with the documented `schemas add` / `roles add`, then re-read their lists. A created organization/profile does not imply a usable project or effective user access.
2. For classification `msgclass create`, supply the intended parent ID and approved name/key data. `list/show` supports paging or depth; depth0 is current node,1 direct children,−1 all descendants. Do not mistake a bounded response for the full hierarchy.
3. For `msgclass update`, the source limits changes to name/displayName/keyCode. Deletion removes the selected subtree and is blocked while referenced by organizations/projects/models. Review consumers and approved deletion scope rather than force-removing them.
4. `roles create/delete/list` and `capabilities add/list/remove` require the documented Detection administrator role. Role deletion also removes organization relationships. Role membership, organization association and project membership are separate gates; never grant broad capabilities merely to make another command succeed.
5. Organization deletion removes its Developing profile schema and associations and is blocked by documented dependants. Use [asset configuration](configure-assets.md) and [administration](detection-administration.md) for scope, dependency and recovery checks. There is no automatic restore guarantee.

JSON request shapes and argument placement must come from installed help and the exact service contract. The source has garbled quotation/column examples; no malformed source payload is reproduced here.

## Create, apply and migrate schema templates

The `sas-detection-message-schema-cli` family distinguishes template creation from application:

- `create-template --file` registers a template without creating/updating/deleting instances; `apply-template --template-name` performs its operations. The template name is its internal name, not the filename. `apply-local-template --file` combines creation and application and therefore has write effects.
- A template can be successfully applied only once. After edits, create a new template; for failed application inspect current instances and failure status before retrying. Do not assume atomicity or absence of partial changes.
- `get-templates` supports filter, sort, start and limit (documented default100); inspect application status. `delete-template` removes the selected template, not an established rollback of resulting instances.
- `generate-template-from-database` reads SFM segment/field definitions and produces separate static and user-variable templates; protect database credentials and outputs. `generate-template-from-excel` requires the expected Segment Definitions sheet and layout. A renamed/altered sheet can break conversion.
- Product/tenant selection must be explicit when ambiguity matters; the source's tenant-default wording appears copied from product text. Do not infer tenant routing from that wording. `prepare-transfer-request` prepares export-request JSON, not completed export/import or semantic migration.

Inspect intended create/update/delete operations before application and reconcile saved schema versions/associations afterward. Do not confuse this older template path with the separate [SFM manifest migration](fraud-management-migration.md) procedure.

## Operate List Data without conflating states

The source's plugin route is `sas-viya listdata`. Resolve exact list/revision IDs, key types/order, current publication/expiry and authority. Use [Advanced lists](advanced-lists.md) for the business/dependency contract.

- `create` accepts JSON via data/file and an optional parent folder. Import defines/populates columns using the documented import family; confirm resulting keys/types/counts and any job before publication.
- `get` covers definitions, contents, consumers, revisions/state and lists; use paging. `exists list/contents` answers existence, not correctness. Key-column `search contents` combines multiple criteria with AND. `--privileged` reveals masked values and requires separate authority.
- `revisions create` makes the previous revision immutable; `contents` / `contents-compare` select revision identities (comparison uses repeated `--revision` values); `revisions deploy` changes the chosen runtime-accessible revision. Revision publication and row publication are distinct.
- Row-state commands and expiry `at/in/delete` require intended list/rows. Absolute expiry uses documented UTC or epoch representation; relative expiry is seconds. Verify the configured timezone/unit and actual row state. Removing expiry is not deleting the row.
- `update contents/list` changes selected content/properties; `delete contents` deletes specified rows; `purge-rows` removes all contents; `delete list` removes the list. Do not silently broaden a row repair to purge. The older CLI exposes property updates such as name/immutable that newer UI documentation restricts; source-version disagreement is not a bypass permission.
- The documented deletion `--force` is for incorrectly marked running import/purge jobs, not a general safe recovery option. Retain job IDs and inspect current state before any retry, cancellation or destructive recovery.

Return exact supported command/option evidence, approved scope, sanitized inputs, target/asset/revision/job identities, observed results and unresolved partial effects. No command, authentication or live request is executed during document authoring.
