# Orchestration and alert-feedback boundaries

Load when enrichment, timeout handling, queued delivery, stand-in responses or alert feedback affects strategy acceptance. Conceptual basis: deck slides 43–50; see [source limits](../SOURCES.md). BOSS is identified in the deck as SAS Business Orchestration Services; its presence, adapters, persistence and guarantees must be established on the target. This is a review procedure, not BOSS configuration or an integration API contract.

## Trace one event end to end

1. Map supplied event → validation/transformation → enrichment → detection → client response → alert/analyst feedback → downstream fulfillment. Identify actual systems, schema revisions, correlation identifiers, data owners, approved logging/redaction and evidence locators. Do not assume every stage is installed or every event produces an alert.
2. For each enrichment, record required/optional status, source, freshness, missing/error policy and owner-approved latency budget. Separate request timeout, system SLA and business decision. No default timeout, last-known-good reuse, fail-open or fail-closed policy is supplied here.
3. If the selected design uses store-and-forward, record what is queued, delivery acknowledgement, downstream effect identity, retention and reconciliation owner. Establish duplicate handling and ordering from target evidence. A retry can repeat profile changes, alerts or payment effects; do not replay solely because the caller timed out.
4. If it uses stand-in/one-behind behavior, record the source and age of the substitute response, permitted conditions, authoritative decision, client override and late-response policy. A substitute response is not evidence the original transaction completed detection or that historical approval is appropriate now.
5. Trace alerts separately from transaction decisions. Record alert and message identifiers, strategy/queue revision where supplied, assignment, disposition, message-level marking/reason and timestamps. Separate analyst observation, asynchronous submission, persisted marking, accepted downstream feedback and completed fulfillment. Confirm each using its own evidence, not an HTTP success or UI label alone.
6. For customer contact, unblock/block, payment release, list update or other external action, require the separately authorized owner and exact effect contract. Prepare an acceptance plan offline; do not send contacts, modify lists or act on payments as a consequence of selecting this skill. Detailed investigation/entity-resolution procedures require their own actual product evidence; alert triage is not automatically a full case-management workflow.

## Synthetic acceptance cases

| Case | Required distinction and evidence |
| --- | --- |
| Enrichment absent or stale | Approved missing/stale policy versus an assumed zero-risk value |
| Caller times out after detection | Observed detection/state/effect identity before any replay decision |
| Queued delivery repeats | Event identity, effect count and documented duplicate policy; no presumed exactly-once guarantee |
| Stand-in then late result | Response provenance, authority and reconciliation without a second unauthorized action |
| Marking accepted but fulfillment fails | Separate marking persistence and failed/pending fulfillment status |
| Analyst changes an assessment | Revision/history and feedback availability time, without overwriting provenance |

Return a stage/effect ledger with expected and observed status, missing contracts, responsible owners and the next permitted reconciliation action. Do not promote an analyst marking directly to a mature training label; governance review must establish outcome definition, selection effects and maturity from evidence.
