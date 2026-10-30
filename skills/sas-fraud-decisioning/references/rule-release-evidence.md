# Rule release evidence

Load for rule phase transitions, impact testing or release readiness. Conceptual basis: deck slides 38–42 and 53–54 plus the official SAS rules-writer learning path; see [SOURCES.md](../SOURCES.md). Exact state names, transitions, permissions and deployment artifacts need release-matched evidence.

## Keep design state separate from runtime state

The deck describes Coding, Testing, Production and Recycle, with pending approval/deployment distinctions. Treat these as source-described vocabulary to map to the actual target, not a workflow API enum. In particular, the deck describes Production rules as available for inclusion in the next deployment file: it does not prove deployment or active traffic. Do not infer that a testing operation is side-effect-free on a particular environment from a high-level slide.

1. Record current and proposed rule IDs/revisions, phase, editable state, dependency revisions and environment from supplied evidence. Preserve prior evidence rather than assuming the same display name denotes the same version.
2. Fix the release candidate and policy acceptance table: inputs, profile initial state, rule order, expected fired rules, decision, alert, profile changes and external actions. Include below/at/above thresholds, overlapping rules, missing score/list/profile, unrelated classifications and stateful repeat/late-event cases. Thresholds and expected actions come from the approved specification, not this reference.
3. Compare incumbent and candidate on the same eligible events and initial-state basis. Separate isolated rule impact from combined-strategy outcomes; overlapping rules can fire without changing a final decision. Retain event counts, exclusions, rule-firing counts, decision changes, alert workload and mature-label outcomes where available. Unlabeled or selectively reviewed events do not establish false-positive or loss-reduction claims.
4. For any requested transition, record approver authority, evidence locator, decision/date, exact candidate and unresolved exceptions. No agent recommendation or model approval substitutes for rule/strategy release approval. Mark model-development checks not applicable for a rules-only change, with rationale.
5. For separately authorized deployment, require artifact identity and dependency manifest, selected destination, observed deployment state and active revision evidence. Retain approved rollback/reconciliation ownership and distinguish restoring a rule revision from undoing profile or external effects; do not invent an automatic rollback command.
6. Report business-case results only after observing the actual selected runtime path on authorized cases. Prepared fixtures, accepted submission, artifact build, installed artifact and correct business output are separate statuses. Where no runtime exists, deliver the complete review with deployment and business execution unverified.

## Result fields

Return candidate/dependency identities; phase-to-runtime evidence; expected-versus-observed case table; incumbent comparison population and limits; approvals/exceptions; deployment/active-revision status; and unresolved state/effect reconciliation. A missing required approval is missing, not passed; a supplied result contradicting the approved case is failed.
