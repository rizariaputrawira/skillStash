# Detection administration and model-package operations

Load when administering Detection services, impact-analysis storage or credentials, organization Advanced Settings, alert types, model mappings or score-match diagnostics. When implementing a Python model/signature package or its runtime deployment, also read [Python model packages](python-model-packages.md). This is an original operational synthesis for an authorized SFD administrator or model-integration engineer, not permission to install, authenticate, connect, change configuration or execute models.

## Applicability and ownership

Source: **SAS Detection: Administrator’s Guide**, **2026.03**, March 17, 2026, `v_032-P1:frdag`, printed pp3–134. Page numbers below are printed pages, not physical PDF indexes (the substantive body has an offset of four). The supplied copy has extraction gaps in diagrams, screenshots and icon-only controls; actions below name their purpose or resulting dialog rather than inventing icon labels. Match installed release and the site's deployment files before executing. Official documentation entry: [SAS Help Center](https://documentation.sas.com/), selecting this title and release.

Use canonical owners rather than recreating their procedures:

- For role scope, classification import/add/edit/delete, organization/project creation, schema associations and profile editing, read [Configure assets](configure-assets.md). The administrator manual's project-navigation tip prints `sda.navigation.organizations`; its capability table identifies **`sda.navigation.projects`**. Resolve target capabilities rather than copying the inconsistent tip.
- For Advanced List definition/data/revision/publication/expiry/association operations, read [Advanced lists](advanced-lists.md). Service/credential configuration belongs below, not in a replacement list recipe.
- For rule promotion, model association, release approval, deployment/load and runtime acceptance, read [Rules, testing and deployment](rules-testing-deployment.md). A saved association, a deployment package and a running model are different results.
- For SFD message-type/queue mappings, manual alerts, investigation and disposition, read [Alert triage](alert-triage.md). Detection alert types below do not configure that entire downstream workflow.

Record requested operation, actor/capabilities, release, namespace/organization, current configuration/asset revisions, approval and intended finish condition. Prepare inputs and expected observations when execution is unavailable; mark all live operations unperformed.

## 1. Prepare impact-analysis storage and protected results

Source pp3–7. These changes affect compute infrastructure and future result data sets; coordinate with the platform/storage owner.

1. Start from the installed `$deploy/sas-bases/examples/sas-compute-server/configure/compute-server-add-nfs-mount.yaml`, copy it into the site's configuration directory, and fill the existing placeholders. Set mount name to `sas-detection-definition-volume`; put the actual approved NFS host/path in the volume; set the container mount path to `/detectionRepository`. Replace the entire brace-delimited placeholder, not just its text. Add the customized file to the transformer block of the base `kustomization.yaml` using the site's deployment process. Do not use the manual's example server or path as a live destination.
2. Verify the approved compute server can use the persistent location and that it survives the site's normal pod lifecycle. This is an operational acceptance recommendation, not a persistence test performed by this reference.
3. If password-protected impact-analysis data sets are required, obtain a compliant password through approved secret handling. The documented SAS data-set password has at most eight characters, is case-insensitive and is a valid SAS name. Have the authorized SAS operator encode it with **PROC PWENCODE**; encoding is not a claim of strong encryption or an instruction to print a secret in reports.
4. Create a password-type authentication domain with the supported credentials CLI; add client credentials for identity **`sas.detectionDefinition`** using the encoded password. Do not reuse the manual's sample domain/password. In **Organizations → organization → Advanced Settings**, a **SAS Administrator** selects that domain in **Message dataset password credential** and saves. The selector contains domains with that service identity.
5. Confirm the selected domain, then create an authorized synthetic impact analysis and verify the intended read path. Protection applies to **future** impact-analysis results, not documented retroactive protection. Detection retrieves the stored password when showing the data set; another SAS program must supply it. Deleting the domain can make those result data sets unreadable: preserve credential lifecycle/retention ownership before removal.

**Finish:** approved mount/configuration identity and observed persistence/read result, selected credential-domain identity, scope of newly protected results and any unperformed check. No backup/restore or password-recovery guarantee is established here.

## 2. Configure List Data credentials and Redis

Source pp4–6; the release-matched *SAS Viya Platform: Configuration Properties*, **List Data Service**, owns the complete current configuration schema. The administrator guide does not supply every multi-Redis property.

1. In **SAS Environment Manager → Domains**, create a password-type authentication domain if needed; the documented List Data domain is **`ListDataRedis`**. Add credentials for the actual authorized identities, with **Identity**, **User ID** and **Password** in **New Credential**, then **Save**. The guide gives `SASDetectionAdvanceListAccess` and `SASAdministrators` as examples, not a mandate to add every user. Its tip says a domain with credentials already configured might not appear among selectable domains; inspect existing state before creating duplicates.
2. Through the supported credentials CLI, create client credentials in that domain for both **`sas.detectionDefinition`** and **`sas.catalog`**. Fill domain ID, identity ID, Redis user and actual secret through approved handling; do not copy the manual's `default/password` example as a credential policy.
3. Open **Configuration → All Services → List Data service → `sas.listdata.redis`**, edit the authenticated Redis datasource address and SSL choice, then **Save**. Match server credentials and TLS trust with the actual Redis installation. Advanced Lists can use multiple Redis instances from 2025.10; select the release-supported storage configuration rather than extrapolating a single-Redis example.
4. Verify an authorized list operation through the canonical [Advanced lists](advanced-lists.md) procedure. Keep List Data Redis separate from Detection Definition Redis and Python/engine profile-store configuration; success in one service does not prove another uses the same store.

**Finish:** domain/service identity records without secrets, actual datasource/TLS/storage configuration and authorized observed list-service result. Redis provisioning, certificates and connection trust require the platform owner's release-matched contract.

## 3. Review service messaging and security properties

Source pp9–14. In **SAS Environment Manager**, sign in as the authorized administrator, choose **Configuration → View: All Services**, select the owning service, open the named configuration instance, edit approved fields and save using the target controls. Retain before/after nonsecret values and the service owner; do not blanket-replace unrelated global instances.

| Instance / service | How to populate and check the fields |
| --- | --- |
| `sas.detectiondefinition.kafka`, **SAS Detection Definition service** | Set `broker` to actual repository Kafka host/port. Select **Protocol** from `PLAINTEXT`, `SSL`, `SASL_PLAINTEXT`, `SASL_SSL` (source default `PLAINTEXT`). For TLS provide **CA Certificate Location**, **Keystore Certificate Location** and **Private Key Location** as required by the deployed client contract. Select **SAS Authentication Domain** for the client credentials. For SASL choose **SASL Mechanism** `PLAIN`, `SCRAM-SHA-256` or `SCRAM-SHA-512`; the table explicitly requires **SASL Username/Password** for `PLAIN`. Do not infer SCRAM secret resolution from that limited table: use the installed service schema. |
| `sas.detectionanalysis.kafka`, **SAS Detection Analysis service** | Configure the same broker/certificate/protocol/domain/mechanism fields for the analysis consumer, separately from Definition. The source lists protocol default `PLAINTEXT` and mechanism default `PLAIN`; defaults are not security recommendations. Reconcile topic visibility and credentials with the organization's **Message location**. |
| `sas.detectiondefinition.redis`, **SAS Detection Definition service** | Populate `authuser`, `host`, `password`, `port`, and `logicalDatabase` (documented default `0`) for lookup-list/profile storage. Use the approved store/database; do not assume runtime environment-variable settings automatically configure this service. |
| `sas.detectionmessageschema.user.group`, **SAS Detection Message Schema service** | Inspect `idExclusions`, a comma-separated list of group prefixes excluded from schema access selection; source default `SASAdmin,SDA,DMS`. Change only for approved access-policy reasons, then inspect the selectable audience. This does not grant schema-edit permissions. |

Design-time Viya uses general authorization and SAS Logon authentication (LDAP, OIDC/SAML SSO or Kerberos according to platform configuration). Full-stack design-time TLS does **not** establish end-to-end runtime security: the guide explicitly says full TLS between Kafka and the Detection Engine is not provided by default and recommends configuring it. Kafka and Redis must be supplied/configured/maintained by the site; establish encryption in transit/at rest, Kafka topic retention and Redis retention with their owners. UI data masking is not storage encryption or evidence that exports/logs are redacted.

**Finish:** each affected service's saved configuration and separately observed authorized connectivity/consumption result. No authentication setup, certificate generation, deployment rollout or encryption claim follows solely from inspecting these fields.

## 4. Configure organization Advanced Settings

Source pp73–75. With organization-update authority, open **Organizations → intended organization → Advanced Settings**, change the approved fields below and save. Invalid Detection URL format, deployment expiration, maximum message length or publishing destination prevents saving the configuration; retain the exact error rather than changing unrelated fields to get past it.

| Field | Concrete administration choice |
| --- | --- |
| **Detection URL** | Enter the actual engine processing URL, beginning `http://` or `https://` and containing a host/domain/IP. Use **Test** and retain its status. Without a URL, enable/disable and `restartDetectionService` controls are unavailable; a valid URL is not evidence of business-correct processing. |
| **Automatically deploy changes** / **Auto deployment delay** | Default false. When approved, enable and set the delay in **seconds** after Production promotion. Record the coupled release behavior; do not surprise rule authors with an automatic deployment. |
| **Automatically restart detection services** | When approved, enable restart following successful deployment. Record its observed result separately from registry publication. |
| **Publishing destination** | Choose the approved package destination; documented default **SAS Content Manager**. Container deployment requires the intended reachable registry, not simply the default label. |
| **Deployment history expiration** | Set approved retention in days; default **14**. Retain release evidence outside expiring history when policy requires it; this field is not an engine rollback facility. |
| **Image name** | Start from the generated lowercased organization name with special characters removed, then set a registry-compliant name. It must start with a letter, number or period; permitted characters include letters, numbers, periods, hyphens and single/double underscores. No spaces, other special or multibyte characters. The registry must be accessible to Detection Definition. Reconcile with installed `configuration.env` **`DETECTION_IMAGE`** before deployment. |
| **Schema generation** | Choose **Necessary schema only** (default, portions used by rules/models) or full schema according to the consumer contract. A field echoed in a message is not evidence it was included/evaluated in the deployed schema. |
| **Maximum message length** | Documented default **64,000 bytes**; consult the SAS representative before changing. |
| **Deploy testing rules** | Default false; consult the SAS representative before changing. Read the canonical rule-deployment reference for Testing-rule side-effect limits rather than treating it as a production-equivalent test mode. |
| **Message location** | Enter the intended Kafka message-repository topic for impact analysis; use its separate **Test** control and retain result. This is not a Kafka broker URL or downstream alert queue mapping. |
| **Message dataset password credential** | SAS Administrators select the domain prepared in section 1; default unset. Do not delete a selected domain without assessing existing protected result readability. |

**Finish:** saved settings, both applicable Test results, planned automatic behavior and image/topic/credential identities; deployment and execution remain separate work.

## 5. Create and associate Detection alert types

Source pp61–65. Check `sda.navigation.alert.types`, `sda.alert.types.view`, and the required `sda.alert.types.create`/`delete` capabilities. The role matrix includes update authority but this manual supplies no separate edit-alert-type procedure; use installed release controls for that operation rather than inventing one.

1. Identify the actual **string** alerting-entity variable, associated composite schemas, organization and downstream triage contract. The decision rule must choose an alert type for `detection.Alert`; every alert-recommending message must contain that type's entity variable.
2. Open **Alert Types → New alert type**. Enter **Name** (required, up to 100 letters/digits/hyphens/underscores/spaces; not necessarily unique), **Code** (required, system-unique, up to 15 letters/digits/underscores), optional **Description** (up to 1,000), then select **Alerting entity variable** in its picker. Filter by variable and inspect the composite schemas shown beneath it; select, **OK → Save**. Require the creation message and new table row.
3. Select an alert type and inspect its **Composite Schemas** pane; open the intended schema to confirm the exact path. Predefined code-to-path pairs are `sfd_company` → `company.identifier`, `sfd_creditacc` → `creditaccount.number`, `sfd_creditcard` → `creditcard.number`, `sfd_customer` → `customer.identifier`, `sfd_debitacc` → `debitaccount.number`, `sfd_debitcard` → `debitcard.number`, and `sfd_merchant` → `merchant.identifier`. These identifiers are factual defaults, not evidence a site's active schema/payload contains them.
4. Open **Organizations → organization → Alert Types**, open **Manage Alert Types**, move the approved available types into the selection, **OK**, then save the organization. A type appears as available only if its entity variable belongs to an organization-associated schema. If absent, correct the actual schema association/path; do not substitute a different entity key.
5. Before removal, identify referring rules. Types used in **Testing** or **Production** cannot be removed from the organization or deleted. For an approved association removal select it in the organization's **Alert Types**, use its removal control and save. To delete an unused type, right-click it in **Alert Types → Delete**, confirm **Delete** and retain the success message. These are distinct operations.
6. Author/promote/deploy the approved rule through the canonical [rule procedure](rules-testing-deployment.md), then test populated/missing/wrong-entity and in/out-of-scope messages. Confirm the returned recommendation separately from actual downstream alert creation through [Alert triage](alert-triage.md).

**Finish:** type code/entity path/schema evidence, saved organization association, referring-rule revisions and observed recommendation/triage outcomes (or marked unperformed).

## 6. Maintain model associations and diagnose score matching

Source pp77–89. Use [Rules, testing and deployment](rules-testing-deployment.md#3-if-the-rule-uses-a-model-associate-and-prove-the-model-first) for New model, classifications, variable mappings and deployment. Additional administration details:

- An association **Name** is organization-unique, at most 150 letters/digits/hyphens/underscores/spaces; description is at most 1,000. Open **Message Classification** to maintain applicability; **Properties** to maintain name/description; **Variable Mappings** to maintain mappings only while the model is **not active**. Model view requires `sda.models.view` plus `sda.projects.view` in the cited association procedure. Do not infer write authority from a visible Models entry.
- Inspect all mapping states, not just creation success: complete, in process, erroneous, deployed/running, deployed/reachable-but-not-running, and unknown/unreachable are distinct documented states. The source depicts them with icons, not stable text labels recovered here. Missing/type-mismatched variables require same-type organization schema mappings; changes can affect other associations using the same model, and signature mappings are shared across organization models.
- To remove an association with `sda.models.delete`, select the correct organization in **Models**, right-click the intended association → **Delete**, then confirm. This does not take operational effect until the **next deployment**. Review rules referencing its name and remaining shared-signature mappings before removal; deletion is not immediate traffic rollback.
- Python and DS2 models share the Model Manager registration/organization deployment route. This manual provides the detailed **Python** implementation contract, not a general DS2 model-definition schema or arbitrary SAS-to-DS2 conversion procedure. For a new DS2 package require its actual supported package specification; finish association and test preparation without fabricating a package body.

### Obtain and run the score-match artifacts

For a supplied SAS model with validation artifacts, use an isolated disposable organization. The manual advises deleting that organization afterward; do so only under approved cleanup authority and after preserving required evidence.

1. Ask the platform/model owner to resolve the order/cadence-specific model image using the site's entitled registry tooling. Retrieve that exact image, create an extraction container without executing model code, and copy its model content from its actual `/opt/sas/<image-name>/src` location. ZIP the content and import it into the **SAS Detection Model Repository** through Model Manager's supported import route. Names/paths in the manual are model-specific examples, not universal identifiers.
2. Retrieve **INDAT.ION** and **OUTDAT.ION** from the same image/version's actual model folder (the manual's common pattern is `src/Models/<model-name>/<model-name-and-version>/`). Retain hashes/version and keep the paired files together. Remove only extraction resources created for this task through the platform-approved cleanup procedure; do not copy the source's ambiguous image-removal shorthand.
3. Create the isolated score-match organization using the expected classifications, schemas and roles. Configure its registry publishing destination, associate the model and deploy/load it using the canonical owner. The source says no Advanced Lists need be added for its supplied score-match scenario, not that every custom model is list-independent.
4. Run the release-supported `decision_score_match` with actual target, `--in` and `--out` paths through the canonical CLI branch. Its default is stop on first mismatch; `--failOnFirst false` continues. Capture terminal summary and structured failures (`actual`, `expected`, `field`, `messageNumber`, `messageIdentifier`, `referenceIdentifier`), not just progress dots. A dot is printed per 50 processed messages beginning at a match, `x` for mismatches; progress symbols alone do not establish full coverage.
5. Before any **repeat** run, clear only that disposable organization's signature keys from its actual Redis profile store under explicit destructive-state authority. The documented key pattern is `SAS|0|0|SIGNATURE|<Organization>|*`; inspect exact resolved organization/prefix and matched keys before deletion. Never use a live organization or broad database flush. Existing signatures can make score matching fail regardless of numeric outputs.

### Generate a targeted model diagnostic data set

1. Preserve the original mismatch and its 1-based `messageNumber` **N**. Reset only the isolated organization's signatures as above. Re-run the first **N−1** messages with `--limit` to reconstruct prior history; do not jump directly to the failing record with empty state.
2. In the approved Container Runtime deployment, set **`SAS_MODEL_DIAGNOSTIC_DATASET`** to true and **`SAS_MODEL_DATA_PATH`** to the approved output parent; ensure it contains an **`mdldiag`** directory. Apply the deployment update and restart its engine pod using the installed platform procedure.
3. Continue with **`--offset N−1 --limit 1`** against the same artifact/target to evaluate only the failing message. The source example uses message 150 → offset 149; do not treat offset as a 1-based message number. Diagnostics are generated for each evaluated message, so the limit also bounds unexpected success output.
4. Set `SAS_MODEL_DIAGNOSTIC_DATASET` back to false, save and deploy the change/restart. The guide says the data set is not fully written until the engine pod restarts; inspect the completed file only after that transition. Retain the diagnostic file securely with model/build/history identities and exact observed mismatch; do not expose transaction contents or leave diagnostics enabled unintentionally.

**Finish:** association/mapping and deployed-model identities, paired validation-artifact provenance, isolated-state/reset evidence, complete match coverage or exact mismatches, and targeted completed diagnostic location if used. No live scoring or signature deletion was performed by authoring this reference.

## 7. Inspect historical deployment contents and retire assets

Source pp119–134. Release execution remains owned by [Rules, testing and deployment](rules-testing-deployment.md#6-deploy-the-organization-package-and-load-it).

- Open **Deployments → intended package**. **Production Rules**/**Testing Rules** show included rule revisions; open a rule for read-only **Rule Summary** only if a member of its project. **Models** exposes Status, Name, Model and Date Deployed; the outer view's **Models Added/Models Removed** describe the latest deployment delta, not every historical build.
- In **Message Schemas**, retain Name, Type, State, Version, Modified By, Date Deployed and Date Modified. To reveal the hidden Date Modified column, right-click a column heading → **Manage columns**, select **Date Modified**, confirm and **OK**.
- Open **History**, select a build other than the current **(viewing)** build, and use its open control to inspect the rules/models/schemas included. Opening history is not a rollback or redeployment. Capture the build while retention allows it.
- For project lookup, select **Projects → organization** and search literal character strings in name/description (case-insensitive, no wildcards). For an approved project deletion select/right-click **Delete**, confirm and retain success. Schema/list associations used by Testing/Production rules cannot be removed; review dependent rules/state before destruction.
- For an approved organization deletion, use its delete control from the Organizations list or opened organization and confirm **Delete**. It cannot be deleted with projects or rules associated. Do not destroy unrelated dependants to force deletion. Credential domains, Redis signatures, external effects and history preservation need their own approved disposition; deleting a UI object is not evidence those systems were erased or restored.

**Return:** operation and authority; saved versus merely prepared configuration; actual source/installed release; exact organization/type/model/package/build identities; expected/observed connectivity, recommendation, score and historical-content checks; failures, unperformed checks, sensitive-artifact locations and the next permitted action. The manual has no appendices after printed p134 and supplies no universal backup/restore, model-state migration or manual restart REST endpoint contract.
