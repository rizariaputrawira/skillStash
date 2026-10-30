---
name: sas-fraud-decisioning
description: "Use for SAS Fraud Decisioning how-to work: configure classifications, schemas and profiles; author/test/deploy rules; manage lists; integrate BOSS; work alerts; build fraud reports; or plan SFM migration."
---

# SAS Fraud Decisioning how-to

For an agent helping an SFD administrator, rule author, integration engineer or investigator carry out a specific task. This standalone package covers the supplied enablement topics with practical procedures, conditional technical references and verification. It is not an installer, product entitlement, approval authority or replacement for the target's release-matched manuals.

## 1. Bind the task and environment

Record the requested outcome and role; exact SFD, platform and component releases; namespace/organization/project; supplied assets and versions; acceptance cases; and permitted reads/writes/execution. Distinguish **SAS Fraud Decisioning**, **SAS Fraud Management (SFM)**, **SAS Intelligent Decisioning**, Detection Architecture, Detection Engine, Alert Triage and BOSS. Product documentation or an MCP connection does not establish installed support or permissions.

Use [SOURCES.md](SOURCES.md) to select the applicable source edition. The practical baseline is primarily official **2026.03** manuals; the Detection CLI and Visual Analytics editions are separate. File/folder labels are not authoritative: use each document's actual cover/title/docset. Unknown versions permit a complete conceptual or offline preparation artifact, but block unestablished syntax/UI compatibility claims and dependent live operations.

Selecting this skill does not authorize installation, authentication, restricted-data access, rule/configuration writes, testing against real transactions, deployment, replay, list changes, contacts or payment/account actions. When authorized, follow the actual task-specific procedure; this is not an offline-only skill. Without authority or a runtime, prepare the full plan/inputs/expected cases and label live steps unperformed.

## 2. Load the matching how-to

Read each triggered reference before using its procedure. For a new end-to-end strategy, work in dependency order: scope and data → profiles/lists/models → rules → tests/approval → deployment → orchestration and alert/report outcomes. Existing installations need only the requested delta and affected dependencies, not recreation of every asset.

| Task / trigger | Required reference | Concrete output |
| --- | --- | --- |
| Set up classification hierarchy, organizations/projects, schema/variable sets, profile keys/variables or access boundaries | [Configure assets](references/configure-assets.md) | Scoped, versioned assets and dependency/association checks |
| Configure Detection services/storage/credentials, maintain organization settings or alert types, or package Python models/signatures | [Detection administration](references/detection-administration.md) | Scoped configuration and model/package contracts, with target-specific validation and effect evidence |
| Write assisted/coded decision or variable rules; test, assess impact, promote or deploy; inspect model/engine/CLI path | [Rules, testing and deployment](references/rules-testing-deployment.md) | Rule/test/model revisions, approval/deployment artifacts and observed engine results |
| Implement detection-specific methods, supported DS2 constructs, array/score/list access or diagnose code-level restrictions | [Rule code contracts](references/rule-code-reference.md) | Release-bound method/type/error contract and synthetic boundary cases |
| Configure or diagnose the engine, Kafka/profile/TLS/sidecar path, loaded builds, runtime returns or a CLI command family | [Engine runtime](references/engine-runtime.md) and [Detection CLI](references/detection-cli.md) | Separate engine/CLI release evidence, approved operations and keyed results |
| Create/import/update/publish Advanced Lists, configure row expiry, associate them or consume membership/lookup results | [Advanced lists](references/advanced-lists.md) | List/row revisions and publication, key/value contract, associations and runtime visibility checks |
| Build an enrichment/orchestration path in SAS Business Orchestration Services | [BOSS orchestration](references/boss-orchestration.md) | Versioned configuration, synthetic request/response checks and actual worker/deployment status |
| Configure SFD alert/message mappings, enable manual alerts, review/mark/disposition an alert or reconcile downstream actions | [Alert triage](references/alert-triage.md) | Alert/message/configuration identities, permission checks, marking/disposition and fulfillment status |
| Create a fraud dashboard/report, prepare a model scoring handoff or use an already available reporting Copilot | [Reporting and model handoff](references/reporting-and-model-handoff.md) | Saved/prepared report with reconciled metrics, or feature/score/artifact acceptance packet |
| Migrate legacy SFM rules/configuration into SFD | [SFM migration](references/fraud-management-migration.md) | Extract/conversion manifest, issues, application-job reconciliation and semantic tests |

## 3. Specify and verify the business behavior

Before changing classification, schema, profile or rule dependencies, read [strategy assets and state](references/strategy-assets.md). Fix typed inputs, entity keys, units, missing/default behavior, profile read/update order, score scale/event and rule/action precedence. Do not treat a numeric score as a probability or a deck rule example as executable product syntax.

For rule phase transitions or a proposed release, read [rule release evidence](references/rule-release-evidence.md). Keep editable design, testing, approval, artifact inclusion, deployed revision and correct business output distinct. A Production label is not proof a rule is running; a model approval does not approve a rules-only release.

When timeout, queued/late messages, stand-in responses, alert feedback or external fulfillment affects a case, read [orchestration and feedback](references/orchestration-and-feedback.md). Preserve request/job/event/effect identities and reconcile uncertain state before replay. Do not infer exactly-once delivery, rollback of profile/external effects or authority for a customer action.

## 4. Execute the authorized path and return evidence

Use the target's documented UI, CLI or service contract and actual available tools. A suitable configured MCP tool may implement a supported operation, but do not map Intelligent Decisioning rule sets/lookups or MAS publication onto SFD rules/lists/deployment without explicit capability evidence. Do not switch transport to bypass deliberate filtering or a permission denial.

For each affected path, retain approved synthetic inputs, initial state, expected output/state/effects, observed result and exact asset/artifact revisions. Check below/at/above thresholds, missing/unknown values, out-of-scope events, overlaps, stateful repeat/late events and partial failures as applicable. Source-derived safeguards are test plans until exercised; neither a saved object nor an accepted job proves business correctness.

Return: task and authority; actual environment and source applicability; performed versus prepared steps; asset/dependency revisions; expected-versus-observed checks; job/deployment/runtime identities; alert/report/fulfillment outcomes where applicable; failures, missing evidence and next permitted action. Keep credentials and customer payloads out of reports. State live execution and host discovery unverified when not exercised.

## Coverage boundary

This package owns SFD how-to integration, not a duplicate modeling textbook or general SAS/REST client setup. Model fitting, model governance and full investigation/network-analysis work remain distinct tasks under their actual product evidence; a reference to Visual Investigator in a solution slide does not establish a case-management procedure. Planned Copilot concepts in the deck are not a current feature contract. If the requested control or API is absent from the consulted edition, retrieve the owning official SAS guide or supplied target contract; finish reachable preparation without inventing the missing operation.

When asked for exhaustive source coverage, distinguish text inspection, visual/media inspection, semantic review, incorporation and execution. A cited source or a successful extraction is not a fully reviewed document. Record every source section/reference item, its maintained guidance owner or justified source-bound disposition, and exact remaining gaps. Do not mark a manual complete while any table/footnote, example, image, video/audio component or relevant technical contract remains unreviewed.
