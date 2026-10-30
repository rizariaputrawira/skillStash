# Configure and work SFD alerts

## Applicability and prerequisites

Source: supplied official *SAS Fraud Decisioning: User’s Guide*, **2026.03**, March 17, 2026, `v_028-P1:frdpayug`; consulted printed pp.109, 111–115 and 183–199. Source identity and limitations: [SOURCES.md](../SOURCES.md). These are SFD-specific content and configuration procedures, not a complete Alert Triage administration manual. Confirm installed release, actual permissions, domain, alert types and downstream integrations before applying them. Configuration and customer actions require separate explicit authority.

## Configure the alert/message contract

1. Choose the entity being alerted on and bind its alert type to the message schema and populated entity identifier. Do not use the display label as a key or assume an account alert and transaction alert have identical scope. Record organization and source decision/rule revision.
2. For each message type, prepare its unique `ID`, `displayNameLocalizationKey`, `defaultDisplayName`, `messageSchemaName`, `discriminators`, `indexableVariablePaths`, `transactionTypeTableName`, `transactionDatetimePath` and `transactionIdPath`. Resolve paths against the actual schema and validate populated identifiers and timestamps on synthetic messages. The manual's extracted example has malformed punctuation/casing; it is not copied here as executable JSON. Use the installed configuration contract to serialize/apply these fields.
3. Review the domain → triage type → queue mapping, routing priority and service priority. Test intended queue, overlapping conditions and unmatched cases. Routing priority and analyst servicing priority are distinct fields. Do not install the manual's illustrative routing expressions as bank policy: validate card-present semantics, entity type, amount units and approved thresholds.
4. Check the analyst's actual group capabilities and access to the relevant domain/queue; an SFD role label is not proof of effective access. The SFD guide names Decisioning Investigator and Decisioning Sr Investigator for investigation work and separate administration/authoring roles. Change group membership only under administration authority.
5. Verify a synthetic alert exposes the expected entity, transaction identity, timestamp and relevant fields without unmasking unnecessary sensitive data. Return message-type/configuration revisions and observed routing; if only a specification was prepared, mark configuration application and UI checks unperformed.

## Enable manual alerts

This is an administrator change, not a workaround for a missing button. In the 2026.03 guide, the prerequisites are:

1. Provision the approved Kafka topic dedicated to manual-alert transactions.
2. Set `SAS_TRIAGE_KAFKA_MANUAL_ALERT_TOPIC` in the relevant Kubernetes ConfigMap to that topic, using the site's supported deployment/change process.
3. Set each permitted alert type's `actionableEntityVariablePathName` to the entity-key path. Confirm the value is present on eligible messages.
4. Configure each eligible transaction type's `alertTypes` with its allowed alert types.
5. In SAS Environment Manager, grant `triage.alert.alert_creation` to the specifically approved groups.

Verify all five together on authorized synthetic cases: permitted user/type/populated key can create the intended alert; missing key, unconfigured transaction type and unauthorized user do not gain access. Observe the created alert identity and topic/integration result. The consulted guide does not supply a universal ConfigMap name, kubectl command, rollout policy or manual-creation click sequence; obtain those from the target rather than inventing them.

## Service, mark and disposition

1. Open an assigned alert through the site's permitted direct/priority servicing path. Confirm domain, queue, entity and message history before selecting a transaction. Exact servicing controls require the installed Alert Triage user guide; the public learning-path video was not consumed.
2. Review the relevant messages and supporting evidence. Choose a message marking appropriate to the actual certainty. The guide distinguishes suspected from confirmed fraud/genuine, a review-needed state and clearing a prior mark. Do not turn suspicion into confirmation; retain the source and time of any correction.
3. Choose the alert disposition separately from message markings and then select its reason code. Add a factual comment under local policy. A disposition operates at the alert level; markings refer to particular messages. Queue movement, suspension/rescheduling and closure are not equivalent outcomes.
4. Inspect the disposition's configured action group before submitting. Outbound contact, account/card blocking and transaction release can invoke external effects; choose them only with task-specific authority. A block-code label or successful submission is not evidence the external system applied it.
5. Record alert/message IDs, marking/reason and disposition revisions, assignment/queue changes, timestamps and downstream result. For asynchronous posting or uncertain effects, follow [orchestration and feedback](orchestration-and-feedback.md) before considering a retry. Stop at an offline recommendation when action authority is absent.

## Troubleshoot missing history or report fields

For the guide's Kafka output-filter feature, inspect the active BOSS filter and topic contracts before assuming data loss in the UI. The described Alert Triage flow removes profile variables by default, retains selected model/rule information and does not support retroactive backfill when profile variables are added later. The impact-analysis topic is described as removing profile and rule-firing data. These are **2026.03 documented feature behaviors**, not promises about every site's current topics or retention. Check actual retained fields, retention and earliest available evidence before making a historical comparison. Configuration instructions for this feature belong to the release-matched *SAS Fraud Decisioning Command Line Interfaces* guide; the older Detection CLI reference is not automatically equivalent.

## Result

Return configured/prepared contract, actual permissions and authority, synthetic routing/manual-alert checks, marking versus disposition outcomes, downstream fulfillment status, missing historical fields and exact unresolved controls. Never report a manual alert, marking or payment action as completed without observing that layer.
