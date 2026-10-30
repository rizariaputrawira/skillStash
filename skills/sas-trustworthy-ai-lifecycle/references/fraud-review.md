# Fraud model review

Independently authored Skillstash domain guidance. These are review questions and metric definitions, not SAS-supplied policy, default thresholds or evidence of regulatory compliance. Use with the [lifecycle evidence record](lifecycle-evidence.md).

## Bind the decision being evaluated

Record whether the model scores a transaction, customer, account or other entity; what the score means; which rule/threshold consumes it; and whether the resulting action is allow, decline, hold or analyst review. Separate model prediction quality from the outcomes of the whole decision flow, including rules, lookups and human intervention. Record model, feature, rule, lookup and threshold revisions relevant to the review without assuming Fraud Decisioning and Intelligent Decisioning interfaces are interchangeable.

## Evaluate the right population and labels

- Record event-time and label-availability cutoffs. Chargebacks or investigator outcomes can arrive later than the decision; do not treat an unresolved case as confirmed legitimate merely because no fraud label has arrived.
- Define the positive class and ground-truth source, label maturity policy, observation window, cohort and exclusions. State how disputed/corrected labels are handled. Separate mature-label evaluation from provisional operational indicators.
- Check temporal leakage and related-entity leakage against the intended use. Record whether each feature was actually available at decision time. Explain test split, sampling and class weighting; a sampled dataset's event rate is not automatically the production prevalence.
- Only calculate metrics where denominators and labels are available. Show counts, window and uncertainty; undefined precision or recall is not zero. Do not infer performance from accuracy alone in a rare-event population.

## Proposed metric families, not required targets

For predicted fraud as the positive class: precision = TP/(TP+FP), recall = TP/(TP+FN), and false-positive rate = FP/(FP+TN). Publish the class definition and denominator population with each value. Monetary measures require explicit loss/recovery definitions, currency, time window and attribution assumptions; do not call all flagged transaction value prevented loss.

Consider the following only where relevant to the agreed use:

| Review area | Evidence to request |
| --- | --- |
| Missed fraud | Recall and false-negative outcomes on an adequately matured cohort; label coverage and delays |
| Customer friction | False-positive rate, legitimate transactions declined/held, and review/appeal outcomes with defined denominators |
| Operational capacity | Alert/review volume, queue age and processing capacity; an otherwise useful threshold can overload investigators |
| Business utility | Loss and investigation cost definitions, intervention tradeoffs and baseline comparison; no invented monetary benefit |
| Stability and affected groups | Data/score drift and outcome differences across approved business segments; legally sensitive grouping needs approved data access and risk/legal direction |
| Integration | Missing/invalid features, timeout or downstream failure behavior, and actual decision outputs at boundary cases |

Select thresholds with the named model/risk/business owners. An improvement in one aggregate metric does not resolve customer-harm, capacity or segment-level failures. Protected or sensitive attributes must not be collected or inferred merely to complete this template. Document unavailable segment evidence and its implications for the review.

## Make the monitoring plan actionable

When implementing or reviewing a Model Manager custom KPI, read [Model Manager Fraud Ops KPI reference](model-manager-fraud-kpi.md); the label, denominator, owner and threshold rules remain here.

For each selected metric, bind the [monitoring row](lifecycle-evidence.md#3-monitoring-and-escalation-plan) to a cohort, outcome lag, threshold owner and review action. Distinguish fast operational indicators from delayed outcome-based validation. Record the authorized response to a breach or missing measurement, such as investigation, restricting use, proposing a threshold change or considering a previous revision; do not automatically change a decision threshold, retrain, roll back or publish from a governance review.

## Review rule-release and alert-feedback evidence

Use this branch for offline governance of a rules-only release, alert-derived feedback, fulfillment evidence or a rule-impact comparison. Review supplied records; do not design or execute Fraud Decisioning rules, orchestration or deployment. The enablement deck motivates the distinctions below but does not establish a current product state machine or API contract.

1. **Bind a release separately from a model approval.** Record release ID, candidate/baseline rule and strategy revisions, changed dependencies, intended decision/action scope, test population, accountable release owner and required approvers. Link model validation only when a model or its use changes; otherwise mark model-only evidence not applicable with a reason. Capture rule test results, approval records and deployment/activation evidence separately, each with revision, timestamp and locator. A displayed lifecycle state or approval is not proof of deployment, activation or customer outcomes. If no deployment was authorized or observed, report offline readiness only.
2. **Trace feedback without collapsing event stages.** For each sampled alert/message, record pseudonymous join IDs, event time, originating decision/rule revisions, selection/routing mechanism, analyst or automated disposition, marking/reason revision, marking timestamp, posting acknowledgment, downstream receipt, authoritative outcome source/time and reconciliation status. Distinguish a recorded marking, an asynchronously posted event, a consumed event and confirmed fraud/legitimate feedback; none alone proves all later stages. Show unmatched, pending, duplicate, corrected and conflicting records separately. A missing receipt is missing evidence, not a demonstrated delivery failure; an explicit rejection or contradicted criterion is failed evidence. Do not infer retry, ordering, deduplication or delivery guarantees from the deck.
3. **Assess feedback coverage before reuse.** Apply the population and label-maturity rules above rather than treating alert markings as a new ground-truth definition. Record counts from eligible events through alerted, assigned, reviewed and outcome-confirmed subsets, with selection/routing changes and exclusions. Identify how thresholding, prioritization, analyst availability, automatic disposition or customer response affects which outcomes become observable. Report unreviewed and unresolved subsets and whether non-alerted outcomes can be measured. If selection bias cannot be bounded, qualify generalization to the wider population; do not convert reviewed-alert precision into population recall or treat absent feedback as legitimacy.
4. **Make impact comparisons interpretable.** For candidate and baseline, provide rule/strategy revisions, evaluation mode (offline replay, scenario, or observed operation), cohort definition/size, event and outcome cutoffs, exclusions, overlap/multiple-rule attribution, metric numerator/denominator, label coverage and evidence locator. Reuse the metric definitions above. State whether both ran on the same eligible events and dependencies; otherwise identify population, routing, volume and label-maturity differences and limit causal claims. Report absolute counts alongside rates and distinguish flagged value, confirmed loss, recovery and actual intervention outcomes. A dashboard title, demo report or screenshot without its underlying cohort/calculation evidence cannot establish improvement.
5. **Keep customer actions and fulfillment separately authorized.** For proposed or observed contact, block/unblock, payment hold/release or list change, record action type, approving authority, approved scope, target revision, authorization locator, request time, downstream result/receipt, effective time and reconciliation evidence. Distinguish requested, acknowledged, applied, failed and unknown effects. A fraud marking or release recommendation is not authorization to contact a customer or change an account, payment or list; a successful post is not proof of fulfillment. For queued, timed-out or stand-in paths request evidence of the response actually used and the later outcome, without assuming product delivery behavior. Missing authorization blocks the proposed action; this review issues no live requests.

### Offline review result

Return one row per applicable gate (rule testing, release approval, deployment/activation if in scope, feedback reconciliation, population/label suitability, impact comparison, action authorization and fulfillment if in scope):

| Field | Required content |
| --- | --- |
| Scope | Gate, release/rule/model revisions and applicable cohort/window |
| Criterion | Owner-approved acceptance condition and accountable owner; an absent criterion remains missing |
| Evidence | Dated locator, supplied result, observation stage and unresolved counts; reference protected records without copying customer data |
| Disposition | Evidenced, missing, failed, accepted exception with recorded authority/expiry, or not applicable with reason |
| Consequence | Readiness limitation, required owner action and evidence needed to close it |

Conclude with offline readiness supported/not supported/undetermined against the recorded criteria, separately identifying missing evidence and demonstrated failures. List approval and execution steps still outside this review. No new model approval is implied for a rules-only change, and model approval never substitutes for rule-release or customer-action authorization.
