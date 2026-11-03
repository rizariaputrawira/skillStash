# Rule release evidence

Load for rule phase transitions, impact testing or release readiness. This reference owns preparing the operational acceptance cases, executing separately authorized stages and assembling revision-bound evidence for owner approval and independent governance review. Conceptual basis: deck slides 38–42 and 53–54 plus the official SAS rules-writer learning path; see [SOURCES.md](../SOURCES.md). Exact state names, transitions, permissions and deployment artifacts need release-matched evidence.

## Keep design state separate from runtime state

The deck describes Coding, Testing, Production and Recycle, with pending approval/deployment distinctions. Treat these as source-described vocabulary to map to the actual target, not a workflow API enum. In particular, the deck describes Production rules as available for inclusion in the next deployment file: it does not prove deployment or active traffic. Do not infer that a testing operation is side-effect-free on a particular environment from a high-level slide.

1. Record current and proposed rule IDs/revisions, phase, editable state, dependency revisions and environment from supplied evidence. Preserve prior evidence rather than assuming the same display name denotes the same version.
2. Fix the release candidate and prepare the operational acceptance table below from the approved policy/specification. Specify inputs, profile initial state, rule order, expected fired rules, decision, alert, profile changes and external actions. Include below/at/above thresholds, overlapping rules, missing score/list/profile, unrelated classifications and stateful repeat/late-event cases. If a criterion, threshold or expected action lacks owner agreement, record the gap and request that decision before the dependent execution; do not supply a policy default.
3. Prepare incumbent/candidate comparisons on the same eligible events and initial-state basis, and execute only the authorized comparison mode. Separate isolated rule impact from combined-strategy outcomes; overlapping rules can fire without changing a final decision. Retain event counts, exclusions, rule-firing counts, decision changes, alert workload and mature-label outcomes where available. Unlabeled or selectively reviewed events do not establish false-positive or loss-reduction claims.
4. For each requested transition, obtain and record the designated owner's actual decision: approver identity/authority, evidence locator, decision/date, exact candidate, conditions and unresolved exceptions. No agent recommendation or model approval substitutes for rule/strategy release approval. Mark model-development checks not applicable for a rules-only change, with rationale; leave required rule approval missing until its own record is supplied.
5. Prepare the deployment inputs: artifact identity and dependency manifest, selected destination, approved validation cases and rollback/reconciliation ownership. For separately authorized deployment, execute the target-supported path and record submission/job, deployment state and active revision identities from observed evidence. Distinguish restoring a rule revision from undoing profile or external effects; do not invent an automatic rollback command.
6. Execute business acceptance only on the authorized runtime path and cases; attach actual output/state/effect evidence to the table. Prepared fixtures, accepted submission, artifact build, installed artifact and correct business output remain separate stages. Without runtime or authority, deliver the complete prepared table and evidence packet with dependent stages unrun, not failed or passed.

## Operational acceptance table

Prepare one row per case and execution stage. Retain synthetic fixture status even when a later authorized run uses other approved inputs; a synthetic result is not evidence of live customer outcomes.

| Field | Required content |
| --- | --- |
| Case and scope | Case/stage ID, policy/specification revision, rule/strategy candidate and incumbent revisions, dependency manifest, eligible population and comparison mode |
| Inputs and initial state | Input/fixture locator and revision, synthetic or other approved data status, entity/event keys, profile/list/score state, event order and relevant time cutoffs |
| Acceptance condition | Owner-approved criterion and expected fired rules, decision, alert, state changes and external effects; record missing decisions with accountable owner |
| Execution authority and identity | Authorized scope/owner and locator; target environment, artifact, job/deployment/active revision and event/effect identities as applicable |
| Observation | Actual output/state/effects, observation time and evidence locator; leave unobserved values explicitly unobserved |
| Result | Prepared/unrun, observed criterion met, observed criterion failed, or not applicable with reason; record missing evidence separately from a demonstrated failure |
| Approval or exception | Required owner decision, revision/date/locator and conditions; for an accepted exception, retain actual failed result/gap, authorized acceptor, rationale and expiry/review date |
| Next owner action | Missing/failed item, accountable owner, requested evidence or decision and next permitted action; include unresolved state/effect reconciliation |

## Result fields

Return candidate/dependency identities; phase-to-runtime evidence; the acceptance table; incumbent comparison population and limits; recorded approvals/exceptions; deployment/active-revision status; and unresolved state/effect reconciliation. Identify the exact next stage the evidence supports and which approvals or executions remain outstanding. A missing required approval is missing, not passed; a supplied result contradicting the approved case is failed. An owner-accepted exception records a decision without changing that underlying result, and the packet itself is not agent approval.
