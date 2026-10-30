# Advanced lists: publish data and consume it in a detection rule

Load when a SAS Fraud Decisioning detection rule must test list membership or retrieve list values, or when creating, updating, associating or troubleshooting that list. This procedure distinguishes list definition, data revision, list publication, row publication, organization/project association and running-rule visibility.

## Evidence and prerequisites

**Source facts** below are paraphrased from:

- **A — SAS Detection: Administrator’s Guide**, **2026.03**, March 17, 2026, docset `frdag`, `v_032-P1`, printed pp36–44 (semantics/creation/publication), pp45–46 (withdrawal/deletion), pp49–56 (data import/edit/row deployment/expiry/export/CSV), and pp57–60 (association/removal). Configuration source locator: “Configure the List Data Service,” pp4–6, and the referenced **SAS Viya Platform: Configuration Properties**, “List Data Service.” The configuration procedure itself is not reproduced here.
- **R — SAS Detection: Rules Authoring User’s Guide**, **2026.03**, March 13, 2026, docset `frdrs`, `v_033-P1`, printed pp21–23 (editor), pp33–38 (promotion/inactive lists), pp69–70 (consumption), pp73–74 (`contains`), pp99–100 (`lookup`).
- **E — SAS Detection Engine: User’s Guide**, **2026.03**, March 13, 2026, docset `frdod`, `v_024-P1`, printed pp12–15 (scoring-sidecar profile/list Redis configuration).

Official access: [SAS Help Center](https://documentation.sas.com/), selecting the exact title/release above; Detect Behavior provides **? → Help Center**. The extracted supplied covers do not preserve a verified per-page URL. Source manuals are copyrighted/restricted-audience; the procedures and synthetic scenario here are original synthesis. They need neither a local manual file nor another skill package to be used.

Before modifying a list, obtain the target release, authorized list owner/editor/deployer, organization/project administrators, Redis/storage configuration owner, actual list permissions and a controlled test environment. List creation/publication privileges and privileged access to masked data must be established in the target; this guide does not infer them from a rule-editor role. Obtain the business owner’s approved key/value/expiry contract and affected-rule inventory.

**Version differences matter:** A documents multiple Redis configurations for advanced lists beginning with **2025.10**, and default DS2-based column-name restrictions starting in **2026.03**. The older 2025.05 detection CLI manual is not authority for these newer configuration features. Do not replace the configured List Data service or scoring-sidecar settings with a generic Redis connection recipe.

**Original safeguards** in this reference: record before/after identities, inspect affected consumers before publication, isolate boundary cases, preserve diagnostics and reconcile effects before replay. These are operational recommendations, not claims of atomic publishing or automatic rollback.

**Start artifact:** list name/revision; definition and key order/types; value columns; missing-key behavior; duplicate-key policy; row/list deployment state; expiry/time-zone policy; storage configuration; organization/project associations; consuming rule IDs/versions; authorizations and expected cases.

## 1. Create the list definition

1. Open **Advanced Lists → New list**. Enter a system-unique name; it cannot be renamed later. Add an optional label/description and select the authorized save location. Default location is `/SAS Content/Products/List Data`. Select the correct storage configuration now: A says it cannot be changed retroactively. [A pp36,41]
2. Decide whether to use **Define an empty list** or **Import a CSV file that defines the list and populates content**. For a controlled first release, leaving **Deploy list on creation** unselected preserves a preparation step; publication on creation is available if explicitly approved. **Is immutable** is supported only for creation from a CSV defining both structure and contents. An immutable list cannot later be edited/cleared like an ordinary mutable list. [A pp36–42]
3. Set **Allow duplicate keys** only if the consuming contract and installed lookup behavior explicitly support them. A exposes this creation option but elsewhere describes unique key combinations and does not establish which matching row detection `lookup` would return under duplicates. A deterministic single-row rule lookup therefore needs a unique-key contract; do not guess duplicate resolution. [A pp41,49–50; R pp99–100]
4. If expiry is required, select **Row content has an expiry** at creation. It cannot be enabled on an existing list. Advance to **Columns**, choose actual data types and masks, then choose at least one **Key column**. Capture composite-key order from the created definition/editor signature, not from a guessed alphabetical ordering. [A pp37,41–43]
5. In 2026.03 when column restrictions are enabled, names are 1–32 characters, start with a letter or one underscore, contain only Latin letters/digits/underscores, avoid DS2 reserved words, and are unique case-insensitively. Existing nonconforming columns are not automatically changed until modified. Finish the wizard and inspect **List Contents**, **Properties**, **Columns** and the first revision in **Versions** as available. [A pp37,40–43]

**Finish:** list exists with verified definition, selected storage and creation-only choices. Once data exists, structure/key-column edits are restricted; do not treat deleting/recreating a production list as a harmless rename or type fix. [A p38]

## 2. Populate or update the data

1. For a small mutable list, open **List Contents**, use its add-item control, enter values and **Save**. For bulk input choose **Import**, select the CSV in **Import List Items**, and import. Match the actual column definition and configured delimiter. [A pp49–50]
2. Validate CSV header names explicitly: the first row is treated as a header, so a missing header silently consumes the first data row as column names. The configured delimiter is usually comma but is a List Data service setting. Imported files cannot exceed 2 GB. Currency input is numeric and is truncated after four decimal places without an error; currency codes require the documented three-character numeric ISO code, and Datetime uses `YYYY-MM-DD HH:MM:SS`. Retain raw and normalized values needed to explain boundary comparisons. [A pp50,56]
3. Treat import into an existing list as **upsert**, not replacement: matching keys update values; new keys append. Rows absent from the CSV are not documented as deleted. Compare expected keys/values and row counts with the resulting contents. For a long job, observe **Jobs** until it completes; on failure open that job’s log and reconcile what changed before retrying. [A pp39,50]
4. When editing mutable existing data, operate on the latest revision only. In **Edit List Item** or direct cell editing, change non-key values and **Save**. Status and key-column values are not editable this way; masked values cannot be modified with redactions applied. Do not claim that a saved edit has reached every running consumer until publication and runtime checks finish. [A pp37–39,50–51]
5. Record the displayed revision and its before/after difference. A says only one revision can be deployed and describes selecting displayed/deployed revisions; the detailed new-revision control sequence is not specified in the consulted procedure sections. Use the release-matched **Versions** controls/help rather than inventing a “clone revision” action. A also phrases the default deployed revision differently on pp37 and44; explicitly select and verify the intended revision instead of relying on that default.

**Finish:** expected data is saved, any long job has an observed result, and the exact intended revision is identified. A masked UI does not make exported data safe: owner/admin **Export** writes contents with masked values **unmasked**. Protect exported files accordingly. [A pp55–56]

## 3. Associate it with the organization and project

1. Open the intended **Organization → Advanced Lists**, open **Manage Advanced Lists**, select the list from available lists, confirm **OK** and save the organization. This makes it eligible for the organization’s projects; it is not a proof of project association. [A p57]
2. Open the intended **Project → Advanced Lists**, open **Manage Advanced Lists**, select the list, confirm **OK** and save the project. Only lists already associated with its organization are available. The list is now available to that project’s rules. [A pp57–58]
3. Alternatively, SAS Administrators custom-group members can manage project associations from the organization’s Advanced Lists controls using **Add project associations → Project Associations**. Do not assume a general list editor has that route. [A pp58–59]
4. Open a consuming rule in Coding. Use the advanced-list pane/autocomplete to confirm the actual list and its fields are offered, and save after inserting the needed references. The rule’s advanced-list usage pane exposes referenced/inactive lists. If missing, check both associations, permissions, list publication and resource reachability before changing the rule. [R pp21–23,69–70]

**Finish:** saved organization and project associations plus the actual consuming-rule reference. Associating data does not publish it.

## 4. Publish the intended list and rows

1. Select the intended revision explicitly. In Advanced Lists, right-click the selected list and choose **Deploy**, or in its details use **Deploy list**. Verify deployed list/revision status. Only deployed lists are available to consumers. [A pp36–37,44]
2. For a mutable list requiring row-level control, on **List Contents** select approved rows and choose **Deploy selected rows**, then **Deploy**; or use **Deploy all rows** when that is the approved scope. Inspect row status and any **Jobs** result. A deployed row inside a Developing list does not make the list available: it marks the row for deployment when the list is deployed. Publication at both levels matters. Masked-row publication requires privileged access and the documented redaction toggle. [A pp38,51–52]
3. For an expiry-enabled list, select deployed rows and **Customize row expiry**, choose date/time and **OK**. For all-row deployment with expiry use **Deploy all rows → Customize expiry time with deploy**. Inspect each actual expiry: applying a bulk expiry does not overwrite expiries of previously deployed items. At expiry, an item becomes Archived and is no longer used by consumers; it can remain in Redis and can be redeployed. [A pp39,53–54]
4. Observe a controlled consuming-rule request after publication and compare returned membership/value behavior with the chosen revision, row status and expiry. Publication success alone does not prove every runtime is using the intended storage configuration. E distinguishes profile/signature Redis settings from optional advanced-list Redis settings on the scoring sidecar; have the deployment owner reconcile the actual `SAS_REDIS_LIST_*` configuration when visibility differs. [E pp12–15]

**Finish:** chosen revision and rows are observably deployed with intended expiry, and an authorized runtime check sees the expected data. No cross-engine propagation latency or atomic replacement guarantee was established by these sources.

## 5. Use membership or lookup in a coded rule

1. For membership only, select the actual `lists.<list-name>.contains` method through autocomplete. Supply **every** key in the displayed signature with matching types/order. Its documented return is 1 for a found key combination and 0 for not found. A composite-key match is not the same as finding any one component. [R pp69–70,73–74]
2. For associated values, use `lists.<list-name>.lookup`, again supplying every key. Return 1 means found and values returned; return 0 means not found. Only on the successful branch read actual columns under `listValues.<list-name>.<column-name>`. Define the missing-key business action explicitly; the source does not establish a trustworthy value payload following a miss. [R pp99–100]
3. Select the resulting decision action appropriate to the rule type; save and inspect validation. List-service failure is not equivalent to a legitimate key miss, and a generic validation/resource failure must not be converted to an approval by assumption. [R pp10–11,21–23]
4. Inspect the advanced-list usage pane and active state before promotion. An inactive list prevents Production promotion; it also prevents Testing promotion when the organization deploys Testing rules. Run impact/boundary/release steps in [rules-testing-deployment.md](rules-testing-deployment.md) when changing a rule. [R pp33–38]

**Result:** saved rule with verified method/key signature, successful-lookup-only value usage, explicit miss policy and correct dependency identities. No copied manual rule example or invented general SAS list API is needed.

## 6. Original synthetic consuming scenario and checks

**Scenario specification:** Create a mutable, unique-key list for a test organization. Its actual columns represent account identifier, channel and risk band; account plus channel form a composite key. In a coded decision rule, look up that pair. A found row with the approved `review` band recommends Refer; a miss performs the approved no-list-match behavior. Use fictional keys such as `TEST-A17`; these are not customer identifiers. If expiry is part of the specification, enable it when creating the list.

Prepare this expected-versus-observed table before executing through an authorized deployed-engine path:

| Fixture | Required observation |
| --- | --- |
| Exact account and channel, published row | Successful lookup and intended value-driven decision. |
| Same account, different channel | No composite-key match unless that separate row actually exists. |
| Absent key, then present key, then absent key again | Miss branches do not reuse a previous lookup’s values. |
| Stored key containing leading zeroes | Schema/list key types preserve the approved identifier semantics; no implicit numeric conversion is assumed. |
| Saved but undeployed row/list | Runtime visibility agrees with the separate list/row states; editor visibility alone does not pass the case. |
| Just before/at/after approved expiry | Runtime stops consuming the row according to observed configured time behavior; record clocks/time zones instead of assuming undocumented precision. |
| Updated value for existing key plus a new key import | Upsert changes/additions are correct; omitted old keys remain unless separately removed. |
| List service unavailable | Record transport/resource failure separately from not found; no guessed fail-open decision. |

Retain list/revision/storage identities, input key values, publication timestamps, actual runtime build, lookup/decision observations, expiry evidence and job diagnostics. If execution is unavailable, deliver the populated fixture plan and mark runtime visibility/expiry results unverified.

## 7. Withdraw or repair without misreporting rollback

- To withdraw a list, use **Move to developing** and confirm; its deployed revision becomes undeployed too. For selected mutable rows, use **Move selected rows to developing**. Review consuming rules and current traffic consequences first. [A pp44–45,52–53]
- To correct non-key values, update the permitted latest revision and publish the approved state; repeat affected lookup/boundary cases. For a key-definition/storage change that the product does not permit in place, design a replacement list and migrate all consuming references through the normal release procedure instead of altering an unsupported property.
- Before removal, identify all rule/project consumers. A list used by active rules cannot be removed from a project; an organization association cannot be removed while project associations exist. A deployed or referenced list cannot simply be deleted. [A pp46,59–60]
- Preserve failed job logs, partial input/output counts and the actual before/after state before retrying. Do not assume CSV upsert or publication is transactionally rolled back on error; that guarantee was not established.
- Restoring list contents does not reverse decisions, profile changes, alerts or external actions already caused by those contents. Reconcile the affected interval and owners separately.

**Completion artifact:** list definition/revision and associations; import/upsert reconciliation; list and row deployment/expiry evidence; rule method/key contract; expected-versus-observed runtime cases; failure/recovery disposition; affected decisions/effects requiring reconciliation. Missing permissions, unavailable Redis or an unknown installed release block only dependent execution, not the complete prepared procedure.
