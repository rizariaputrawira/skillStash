# Rules, impact analysis and deployment

Load when creating or changing a SAS Fraud Decisioning detection rule, testing its impact, promoting it, associating a scoring model, or proving that a release is running. This is a practical **2026.03** procedure, with a separately identified **2025.05 CLI** branch. It does not require local copies of the research manuals. Resolve the installed release, permissions and environment before executing its matching branch; an unknown release does not prevent preparing the rule specification and test cases.

## Source and authority boundary

The following source facts are paraphrased from official SAS manuals; section names and **printed** pages are locators, not PDF page indexes:

- **R — SAS Detection: Rules Authoring User’s Guide**, 2026.03, March 13, 2026, docset `frdrs`, `v_033-P1`: states pp1–4; authoring pp10–26; dependencies/order pp30–33; promotion pp33–38; enable/disable pp39–41; recovery/export/import/history pp43–51; impact analysis pp53–67; lists pp69–70,73–74,99–100; model-score access pp95–96. For other coded constructs, use the release-matched Appendix 2, “Functions, SAS Statements, Formats, and Informats Supported in Rule Code,” before authoring them.
- **A — SAS Detection: Administrator’s Guide**, 2026.03, March 17, 2026, docset `frdag`, `v_032-P1`: impact-analysis storage p3; organization settings pp73–75; model association pp78–82; score matching pp83–86; Python registration/runtime boundary p117; deployment pp128–133. Alert entity requirement p61.
- **E — SAS Detection Engine: User’s Guide**, 2026.03, March 13, 2026, docset `frdod`, `v_024-P1`: runtime flow pp1–3; sidecar/profile/list configuration pp12–15; restart/watcher/logs pp23–25; monitoring pp27–31; return codes pp33–36; Ion input/results pp37–40.
- **C — SAS Detection Command Line Interfaces**, 2025.05, May 12, 2025, docset `frdcli`, `v_012-P1`: CLI identity p1; connection/auth/help pp3–6; detection commands pp6–13.
- **M — SAS Message Schema: User’s Guide**, 2026.03, March 13, 2026, docset `frdmsg`, `v_030-P1`: schema versioning and Coding-to-Testing side effects pp31–32.

Official access: [SAS Help Center](https://documentation.sas.com/); in Detect Behavior, use **? → Help Center**, then choose the matching version and title above. The supplied manuals expose this Help Center route but their extracted covers do not preserve a usable deep-link target; no guessed per-page URL is asserted. These are restricted-audience, copyrighted sources; this reference is an original synthesis, not redistributed manual text, examples or screenshots.

**Original operational safeguards:** candidate manifests, boundary fixtures, approval evidence, isolated replay, reconciliation and the completion criteria below are this package’s procedures, not promises made by SAS. They do not grant execution, deployment, data-access or customer-action authority. When preparing a release decision or retaining its evidence, load [rule-release-evidence.md](rule-release-evidence.md).

Before using detection methods, supported DS2 constructs, array indexing, formats or informats in coded rules, read [rule code contracts](rule-code-reference.md). For engine configuration/security/returns, read [engine runtime](engine-runtime.md); for design-time templates, List Data commands or runtime CLI operations, read [Detection CLI](detection-cli.md). These references preserve release and source-review limits rather than treating a manual citation as exhaustive coverage.

## 1. Fix the candidate and prerequisites

Before opening a rule, record:

- target release/environment, organization, project, schema name/version and classification scope;
- author, reviewer/promoter and deployment operator with their actual capabilities;
- rule type and authoring method; intended decision, alert type/entity and reason, profile reads/writes, lists, optional model association, execution order and dependencies;
- incumbent rule ID/version and proposed version, acceptance conditions and approved recovery owner;
- whether **Automatically deploy changes**, its delay, **Automatically restart detection services**, **Deploy testing rules**, and the engine watcher are enabled.

**Source facts:** A rule belongs to one project and one associated message schema; its classification scopes it to that classification and descendants. Only **Coding** is editable. **Testing** and **Production** are authoring states, not proof of a live release. Production rules are candidates for deployment. Variable rules update persisted profile values; decision rules use state and can set variables as well as recommend decisions/alerts. Rule type cannot be changed after saving. [R pp1–4,11–13]

Require an existing project with the correct schema/classification and authorized profile-variable definitions. For a profile update, obtain its entity key, initialization/missing-state behavior, type and sharing permissions from the actual profile definition; do not create an arbitrary variable to silence a validation error. Shared read-only profile variables can be read, not assigned. For alerts, obtain a configured alert type and ensure input contains its alerting entity variable. [R pp20–25; A p61]

For impact analysis, require message history, applicable fraud markings, a usable organization **Message location**, compute storage and project access. Creating/canceling/deleting requires `sda.impact.analyses.update` and `sda.projects.view`; viewing requires `sda.impact.analyses.view` and `sda.projects.view`; navigation has its own capability. Missing history or labels blocks the corresponding impact claim, not rule authoring. [R pp53–55; A pp3,73–75]

**Finish:** a named candidate specification and a prerequisite table with each item available, missing or not applicable. Resolve missing runtime authority/configuration before executing that branch.

## 2. Create or edit in Coding

1. In **Rules**, select the project and **Coding → New rule**. Select **Rule assistant** or **Code editor**, and **Decision** or **Variable**. Enter name/description, choose the associated message schema and classification, and save. Names permit letters, digits, hyphens, underscores and spaces, up to 256 characters; descriptions up to 1,000. Schema/classification selections are constrained by the project. [R pp11–13]
2. To change an existing Production or Recycle rule, select **Create new version in "Coding"** instead of attempting an in-place production edit. The source stays where it was. Only one version of a rule may be in Coding or Testing; promotion of a new version replaces the corresponding existing version in Production. Retain the prior identity and contents before replacement. [R pp30,48–49]
3. Follow the authoring branch below. Save periodically: validation is refreshed **on save**, not merely by opening the rule. The Validation column distinguishes empty rule, syntax error, missing resource and generic validation failure; expose it with **Manage columns** if hidden. [R pp10–11,20]
4. Reopen the saved candidate and record its identity, phase, saved contents and validation result. A syntax-clean save is not an executed business test.

### Rule assistant branch

1. Select a statement type. For **IF**, choose the left operand from a value, message variable, profile variable or function; select a type-appropriate operator; then choose the right operand.
2. Add conditions with the condition menu, and add/nest condition groups using the available assistant controls. Enter each value for a multi-value operator separately and press Enter to commit its token. Use explicit group intent and fixtures for each truth combination; do not infer group behavior from color alone.
3. Specify THEN actions and any required ELSE IF/ELSE behavior. A variable rule supports **Fire**; decision rules expose decision actions. Add independent statements with the outer statement menu when required.
4. When recommending an alert, complete **Alert Configuration** with the configured alert type and optional reason, then save. Preview generated code and compare the operands, grouping, actions and alert configuration against the approved specification. [R pp13–19]

**Result:** a saved assistant rule plus its generated-code preview and expected-condition/action table. This branch is the form-based **Rule assistant** documented here, not an unverified Copilot workflow.

### Coded decision or variable/profile rule branch

1. Open the code-editor rule. Insert actual message variables, profile variables, list fields and available functions from the editor’s discovery panes/autocomplete rather than guessing field paths or general SAS APIs. Use only the detection-supported DS2 subset and rule-type-appropriate methods. Variable rules have `detection.Debug`, `detection.Fire` and `detection.hasRuleFired`; decision methods are not a variable-rule shortcut. [R pp19–26]
2. Implement the approved conditions and actions. For a variable rule, assign the writable profile variable identified in step 1 using the approved calculation; cover first message, subsequent message and a separate entity key. For a decision rule, explicitly distinguish a returned decision from an alert recommendation. `detection.Alert` recommends an alert; it does not prove a case was created downstream. Configure the alert type and optional reason before saving. [R pp11–12,21–26,74]
3. If a rule depends on another rule having fired, use the editor-supported `detection.hasRuleFired` reference to the actual rule ID. View **Rule Dependencies** and ensure the prerequisite is available in the destination phase. Coding can depend on Coding/Testing/Production, Testing on Testing/Production, and Production only on Production. Profile reads create **weak** dependencies that do not block removal of the writer; review them separately. [R pp30–31]
4. Save; resolve syntax diagnostics and missing resources. An unavailable list service is not the same defect as invalid DS2 syntax. Unsupported characters can surface in testing even when authoring appeared acceptable. If another user saved or moved the rule, close it, reopen the current rule, compare changes and reapply only the intended edit. Do not overwrite blindly. [R pp10–11,22–23]

**Result:** saved candidate code, profile read/write contract, strong and weak dependency manifest, validation status and unresolved diagnostics. This guide intentionally supplies no fabricated field path, profile API or general-purpose PROC/MAS invocation.

### Execution order and final outcomes

Variable rules run before decision rules; within **each** type, Pre runs before Main, then Post. Main rules are dependency-ordered. Open **Rule Priority**, choose Variable or Decision, and move only Coding rules with **Move to pre**, **Move to main** or **Move to post**. Explicit priority ordering is available for Pre/Post; save with **OK** and resolve dependency violations. A variable Post rule still precedes decision rules: “Post” does not mean after all decision processing. [R pp12,31–33]

The documented decision order, most restrictive first, is Exclude 60, Pickup 50, Decline 40, Refer 30, Verify 20, Approve 10, no decision 0. A later approval does not simply erase a more restrictive earlier decision. Capture both individual rule firing and the **combined final** outcome. Site-specific prioritization codes and until-dates require their exact method contract and tests; do not extrapolate between similarly named methods. [R pp11–12,77–80]

When a rule consumes an advanced list, load [advanced-lists.md](advanced-lists.md) for creating/publishing/associating the list and checking lookup misses, expiry and runtime visibility.

## 3. If the rule uses a model, associate and prove the model first

This branch consumes an already supported model package; it is not a universal model conversion recipe.

1. Obtain the model’s release/runtime compatibility evidence, model identity/version, input and signature-variable contract, expected score interpretation and approved test vectors. A model trained elsewhere is not automatically a deployable SFD model.
2. Register/import the package in **SAS Model Manager** as required by the model’s supported packaging instructions. In Detect Behavior **Models**, select the organization, choose **New model**, enter an organization-unique association name, select the registered model, choose message classifications and map unmatched input/signature variables. Model association requires `sda.models.view` and `sda.models.create`; navigation is separate. Mapping choices must have matching data types. Save with **Finish**, then resolve every incomplete or erroneous required mapping before release. [A pp79–82]
3. Review shared effects: signature mappings are shared across models in the organization; changing a variable mapping warns that all associations using the same model are affected. Removing the final reference removes a signature mapping. Changing the association name affects rules that reference it through `detection.Score`. [A pp78–80]
4. Use the registered model name when retrieving a score in the decision rule. `detection.Score` **returns** the named model’s existing score from the message’s model-fired results; it is not a generic external scoring call. `detection.scoreIndex` obtains a result-array index. The manual uses inconsistent result-array spellings between these method sections; inspect the actual installed schema/results before addressing array fields directly. Missing-model/invalid-index fallback semantics are not established here. [R pp95–96]
5. Deploy the organization with the model and rule, restart/load the package as in step 6, and inspect the package’s **Models** tab and runtime model description. A mapped/registered model and a deployed-but-not-running model are distinct states. The engine processes incoming messages through deployed models/rules and accesses model/user-variable signatures from the profile store; a rules-only deployment does not require a model. [A pp82,131; E pp1–3]
6. For a supplied **SAS model** with `INDAT.ION` and validated `OUTDAT.ION`, perform the documented score match in an organization created for that purpose, with the expected schemas, publishing destination, model association and deployment. Compare actual output to the supplied validation output using the CLI branch below. On repeated score-match runs, an authorized operator must clear **only that disposable organization’s** signatures first, as described in A pp85–86; otherwise historical signatures can cause failure. Preserve the failure evidence before any reset. Never apply that cleanup to a live organization. [A pp83–86]

**Python-specific boundary:** A p117 documents importing Python model packages into Model Manager’s **Detection Repository**, then the same organization association/deployment process as DS2 models. It additionally requires a Python-runtime container, init container and shared model volume. E pp12–15 documents the scoring sidecar’s Redis/profile/list connections, not a blanket compatibility matrix for every Python model. A Python package must meet the dedicated A chapter 11 model/signature/package contract and the installed runtime’s requirements; this reference does not invent that package or copy its examples. If package compatibility is missing, stop model activation and obtain that chapter plus the installed deployment README and package-specific instructions. Model Manager import details require **SAS Model Manager: User’s Guide**, “Import Models” / “Import Models into a Folder,” which was referenced by A but not consulted here.

**Result:** model association identity, classifications, completed mappings and affected associations, supported package/runtime evidence, score-match result where applicable, and observed model presence/running state. A score threshold test does not replace model score matching or release approval.

## 4. Promote to Testing and run impact analysis

1. Save the candidate and resolve validation/dependencies. In Coding, select the rules and **Promote to "Testing"**. Confirm their presence in Testing and retain the exact tested versions. Also capture the schema/deployment definition afterward: for a schema linked to an organization, this promotion checks schema changes, can create a new schema version, and updates the deployment/organization to use the newest version. This is a definition-side effect, not proof a runtime package was loaded. To repair a rule, use **Return to "Coding"**, edit/save, and repeat testing; Testing is not editable. [R pp33–34,45–46; M pp31–32]
2. Check the organization’s testing-deployment policy. By default, Testing rules are not deployed to the production engine. If `deployTestingRules` is enabled, they can run there: they fire/set the RRF fired array but do not update user variables or recommend an alert. That does **not** establish normal production profile/alert behavior. An inactive list blocks Testing promotion for an organization deploying Testing rules. A pp74 also says not to change **Deploy testing rules** without consulting the SAS representative. [R pp33–34; A p74]
3. In **Impact Analyses**, select the project and **New impact analysis**. Set name/description and notification choice. Add the candidate **decision** rule(s) from Testing/Production, selecting only one version per rule. Coding rules and variable rules are not directly selectable. The run includes dependencies; profile variables are recalculated. [R pp53–56]
4. Choose the history interval in displayed local time. Treat it as **start-inclusive, end-exclusive** and record its time zone. Inspect whether messages exist on the selected dates. Optionally set **Alert limit** and **Outcome limit**; each defaults to 1,000 and permits 1–60,000. Select **Finish**. [R pp56–58]
5. Wait for an observed terminal status. **Completed** is the documented error-free completion; **Alert Limit Reached** or **Outcome Limit Reached** means processing stopped early, not full-window coverage. On Building Failed/Execution Failed, open the **Log** and **Properties**, preserve the error and repair its prerequisite before a new run. Cancel only an eligible active run; cancel before attempting to delete a Building/Running run. [R pp57–59,66–67]
6. Open **Performance**, **Rules**, **Messages**, **Log** and **Properties**. Retain the run ID, selected versions/dependencies, interval, actual coverage/limits, counts, message drill-downs, rule code previews and log. Reconcile selected rules with what the run actually used. For an incumbent/candidate comparison, create separate runs with the same eligible history/label basis and comparable profile recalculation assumptions; one run cannot select both versions of the same rule. [R pp55–67]

**Interpretation safeguards:** Alert totals are entity-based; several alerting messages on one entity can count as one alert. Fired messages, negative decisions and alert recommendations are different populations. An alert-only rule can leave the outcome-based false-positive tile empty. The prose/formula shown for false-positive rate on printed p63 is internally inconsistent, so retain raw counts/definitions and obtain the release-specific correction rather than reproducing that formula as authoritative. Fraud markings and decision predictions are not interchangeable evidence of actual fraud. [R pp60–65]

**Finish:** completed run evidence or a clearly named failed/truncated run; candidate-versus-incumbent comparison and acceptance dispositions. Do not claim a live profile persistence test from a Testing-rule firing or an impact-analysis recalculation.

## 5. Obtain promotion approval

1. Provide the fixed candidate, dependency/order manifest, expected-versus-observed cases, impact result and exceptions to the authorized reviewer. For the evidence record, load [rule-release-evidence.md](rule-release-evidence.md).
2. A user without `sda.rules.promote` requests **Request promote to "Production"** from Testing, selects eligible project reviewers, adds notes and submits. The rule remains Testing while the request is pending; **Promote Requested By** is evidence of a request, not an approval. The documented review capability is `sda.rules.approve.promote`. Users with direct promotion authority need not use this request workflow. [R pp35–38]
3. The authorized promoter selects the reviewed Testing versions and **Promote to "Production"**. Confirm Production versions and inactive-list/dependency checks. Promotion rejects inactive advanced lists. Record whether automatic deployment will begin after the organization’s configured delay. Otherwise, promotion alone does not change the running engines. [R pp37–38]

**Finish:** approval tied to exact versions and observed Production state, with pending/automatic/manual deployment explicitly distinguished.

## 6. Deploy the organization package and load it

1. The deployment operator confirms `sda.deployments.create` and `sda.organizations.view`, plus approved organization **Detection URL**, **Publishing destination**, **Message location** and image identity. A pp129 requires the deployment configuration’s `DETECTION_IMAGE` to match the image created for the organization. Coordinate Kubernetes changes through the installed deployment README under `$deploy/sas-bases/examples/sas-detection`; do not paste a generic YAML template. Test the Detection URL and Message location using their documented **Test** controls where applicable. [A pp73–75,129]
2. In **Deployments**, select the intended organization’s deployment package and click **Deploy**. Observe its Status and **Last Deployment Timestamp**. Alternatively deploy the selected organization from **Organizations**, then inspect its status in Deployments. The unit is the organization package, not an isolated rule file. Confirm its **Production Rules**, any **Testing Rules**, **Models** and **Message Schemas** tabs; use **History** for the exact build’s included assets. A blank last-deployment timestamp means the package has not been deployed. [A pp128–133]
3. Ensure the new package is loaded: SAS Container Runtime pod restart is required after deployment. If **Automatically restart detection services** is configured, observe its successful action. If the watcher is configured, observe it detect the registry version and update/restart the pod. The watcher is disabled by default; its interval/deployment-name/SSL settings must be set deliberately. [A pp129–130; E pp23–25]
4. If neither automatic mechanism is in use, the authorized operator follows the installed, release-matched restart procedure. E p23 says a restart REST call exists and restarts all engines in a Container Runtime deployment, but supplies **no HTTP verb/path/body**. That exact call is an evidence gap here: obtain the installed API/runbook, or use the documented automatic mechanism after approved configuration, rather than inventing an endpoint.
5. On every serving engine instance or approved routing path, obtain runtime package/build identity, schema version, enabled rules and models. Compare against the approved package manifest, not merely its UI timestamp. Independent primary engines can share a profile store; successful verification of one does not prove all are on the same build. [E pp2–3; C pp6–13]

**Finish:** package build/deployment timestamp plus observed loaded identity on the serving runtime(s). Registry publication, restart submission and successful traffic are separate results.

## 7. Execute authorized boundary messages and reconcile results

Use an isolated organization or approved test path with controlled entity keys when testing real profile writes or alert recommendations. Prepare valid **Ion** messages against the selected schema, including `message.request.schemaName`, `message.request.messageClassificationName` and all schema-required variables. REST and CLI are documented for individual testing; Kafka for streaming/batch. Obtain the actual REST contract or configured Kafka topic from the environment instead of guessing it. Extra fields can pass through untouched without rules/models evaluating them; seeing a field echoed does not prove it was recognized. Missing required fields are errors. [E pp21,37–40]

For each case, retain input identity, initial profile/list/model state, actual loaded build, returned `message.sas.system`, final decision, fired-rule information when available, alert recommendation and profile-after evidence. Inspect `message.sas.system.returnType` **first**:

| Return | Interpretation and next action |
| --- | --- |
| `0` | Processing succeeded; now compare business outputs and state with expectations. Success alone does not imply the rule fired. |
| `4` | Runtime error; retry may be possible. Preserve `returnDesc`/`returnDetails` and establish whether state or downstream effects occurred before an authorized replay. |
| `8` | Message cannot be processed; classify/fix the indicated message, package or dependency problem before resubmitting. |

For nonzero returns, do not infer rules/models ran from generated response variable sets. Useful diagnostics include `MFLD`/`MVST` (required field/set), `BFLD`/`BVST` (invalid field/set), `BORG`/`NORG` (classification), `NDEP` (no deployment), `NRED` (Redis), `NKAF` (Kafka), and `BBLD` (bad build; the engine itself retries once). `NMAS` appears as a legacy-named code in E; it is not permission to substitute a generic MAS deployment workflow for SFD. [E pp33–36]

Check Rules runtime status and metrics: **Active**, **Disabled**, **Not present but enabled/disabled**, **Unknown**, and list-service-unreachable have different meanings. Unknown is a failed connection, not evidence of absence. Enable/disable sends flags to the engine; verify the actual resulting state. Inspect exceeded rule execution-time indications and configured **Maximum execution time** rather than assuming default timing fits the rule. Engine logs are in the Container Runtime pod; documented metrics include evaluated/overtime counts and Redis concurrent-write failures. Reconcile these with message results and the test window. [R pp39–41; E pp25,27–31]

### Original synthetic consuming scenario

**Specification, not a SAS example:** In an isolated project, use its actual numeric amount variable and writable per-account counter. A variable rule increments the counter; a decision rule recommends Refer and an alert when amount is greater than 750 and the counter is at least 2. Use Rule assistant for the decision rule and Code editor for the approved variable assignment; use only fields inserted from the environment. These values are illustrative, not fraud policy advice.

| Case | Setup and expected check |
| --- | --- |
| Below/equal/above | Separate entity keys or reset controlled state; amounts 749.99, 750, 750.01 at counter 2. Only the last meets the amount condition. |
| Stateful boundary | Same entity, initial counter 0, two qualifying messages in a known sequence. Observe persisted 1 then 2 and the decision’s actual read point; separate entity remains independent. |
| Missing state/input | New profile key and explicitly missing amount. Compare against the approved initialization/missing-value policy, not an assumed default of zero. |
| Scope | Same values with a sibling classification or different schema. Candidate must not act outside its configured scope. |
| Competing rule | A documented stronger Decline rule overlaps the Refer candidate. Record both firings and final Decline rather than expecting last-write-wins. |
| Testing-only | If testing-rule deployment is deliberately enabled, verify its firing separately; do not expect production profile writes or alerts from that Testing rule. |
| Deployment mismatch | Runtime description lacks the candidate or reports an earlier build. Stop the acceptance claim and reconcile deployment/restart/routing before interpreting business output. |

**Consuming result artifact:** a case table with expected/observed fields, message/build/rule identities, profile before/after, decision versus alert evidence and pass/fail/missing disposition, plus impact-analysis and approval locators. If no authorized environment is available, deliver the complete specification/fixture plan and mark executions, persistence, approvals and deployment **unverified**, not passed.

## 8. Recover without losing state history

- **Bad authoring/test result:** return Testing to Coding, fix, save and repeat affected checks. Keep failed evidence associated with the failed version.
- **Operationally harmful rule:** an authorized Senior Rules Editor can use **Disable** in Testing/Production while investigating, then **Enable** after remediation. Inspect strong/weak dependencies first and verify flags on the engine. Disable does not reverse prior profile writes or downstream actions. [R pp39–41]
- **Prior version needed:** create a new Coding version from a historical Production/Recycle or Versions entry, then perform the normal test/approval/deploy/load sequence. Merely opening a historical version or changing the viewing designator is not evidence of runtime rollback. [R pp48–50]
- **Backup/migration:** Coding/Testing **Export** produces YAML and includes strong dependencies and referenced profile-variable definitions, not the project. Import into the intended project’s Coding phase using **Import**; required variable sets must already exist. Existing same-named profile definitions are not overwritten. Read **Import Rules Summary** for schema/classification/syntax errors and imported IDs; `hasRuleFired` IDs are remapped. Imported rules still require normal promotion. [R pp46–48]
- **State/effect reconciliation:** identify messages processed under the bad or mixed build, profile changes, downstream alert/customer actions and their owners. A corrected rule package cannot retroactively undo them. No automatic restoration/replay guarantee is established by these manuals.

## Optional CLI branch — documented 2025.05 commands

Use only after matching the installed CLI’s help/version and configuring its connection profile and authentication. C names `sas-detection-cli` in syntax but demonstrates the **`sas-viya detection`** invocation below. Do not invent an interchangeable binary/plugin installation or assume this older manual proves 2026.03 availability. Run the installed help for each command first; if it differs, use the release-matched contract instead. [C pp1,3–6,12–13]

The following are original command templates assembled from documented command/option names. Replace the uppercase placeholder strings with actual authorized values; they are not runnable defaults:

```sh
sas-viya detection health --target "ENGINE_URL"
sas-viya detection decision_description --target "ENGINE_URL"
sas-viya detection decision_description_rules --target "ENGINE_URL"
sas-viya detection decision_description_models --target "ENGINE_URL"
sas-viya detection decision_description_profiles --target "ENGINE_URL"
sas-viya detection decision_execute --file "BOUNDARY_MESSAGES.ion" --target "ENGINE_URL"
sas-viya detection stats --target "ENGINE_URL"
sas-viya detection stats_timeline --target "ENGINE_URL"
sas-viya detection decision_score_match --in "INDAT.ION" --out "OUTDAT.ION" --target "ISOLATED_SCOREMATCH_ENGINE_URL"
```

Descriptions expose the deployed build/organization/schema requirements, enabled rule IDs and models/profiles; health alone proves none of their business behavior. `decision_execute` processes messages through deployed rules/models and is **not a dry run**. Its optional `--limit` and `--offset` bound the file subset. Avoid `--schema` or `--messageClassification` overrides in a routing test because they replace message-body values. The score-match command defaults to stopping on the first mismatch; `--failOnFirst FALSE` allows continued comparison. Retain the complete match/mismatch result, not just progress markers. [C pp6–13; A pp83–86]

C explicitly documents `--cacert`, `--cert` and `--key` for the description commands; it does not list them under `decision_execute` or `decision_score_match`. Do not blindly append those options to commands whose installed help has not established them, or disable TLS checks to make a command work. Match the installed client/transport support and approved environment requirements.

**Exact remaining gaps:** no installed release/capabilities/runtime were observed; no standalone one-rule test button procedure is specified in the consulted R authoring sections; the executable test paths documented here are saved validation, impact analysis and deployed-engine Ion execution. No exact manual restart endpoint, general custom-model compatibility matrix, missing-model return semantics, or universal profile rollback/idempotent replay mechanism was established. Obtain the named installed API/manual/package contract for a dependent operation; continue independent authoring and evidence preparation without inventing that surface.
