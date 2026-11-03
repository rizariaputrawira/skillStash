# Fraud model review

Independently authored Skillstash domain guidance. These are review questions and metric definitions, not SAS-supplied policy, default thresholds or evidence of regulatory compliance. Use with the [lifecycle evidence record](lifecycle-evidence.md).

## Bind the decision being evaluated

Record whether the model scores a transaction, customer, account or other entity; what the score means; which rule/threshold consumes it; and whether the resulting action is allow, decline, hold or analyst review. Separate model prediction quality from the outcomes of the whole decision flow, including rules, lookups and human intervention. Record model, feature, rule, lookup and threshold revisions relevant to the review without assuming Fraud Decisioning and Intelligent Decisioning interfaces are interchangeable.

## Evaluate the right population and labels

- Record event-time and label-availability cutoffs. Chargebacks or investigator outcomes can arrive later than the decision; do not treat an unresolved case as confirmed legitimate merely because no fraud label has arrived.
- Inspect the documented positive class and ground-truth source, label maturity policy, observation window, cohort and exclusions. Record how disputed/corrected labels are handled and request absent definitions from the accountable owner. Separate mature-label evaluation from provisional operational indicators.
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

Inspect the thresholds selected by the named model/risk/business owners and their recorded rationale and approval; request any missing decision rather than selecting a threshold in this review. An improvement in one aggregate metric does not resolve customer-harm, capacity or segment-level failures. Protected or sensitive attributes must not be collected or inferred merely to complete this template. Document unavailable segment evidence and its implications for the review.

## Make the monitoring plan actionable

When the assignment explicitly requests Model Manager custom KPI implementation or review, read [Model Manager Fraud Ops KPI reference](model-manager-fraud-kpi.md); the label, denominator, owner and threshold rules remain here. A release-evidence review alone does not authorize KPI implementation or registration.

For each owner-selected metric, populate or inspect the [monitoring row](lifecycle-evidence.md#3-monitoring-and-escalation-plan): cohort, outcome lag, threshold owner and review action. For requested plan preparation, document proposed responsibilities and cadence, leaving unresolved threshold, cadence or response decisions with their named owners; for review, compare the supplied plan and execution evidence with recorded owner decisions. Distinguish fast operational indicators from delayed outcome-based validation. Record the authorized response to a breach or missing measurement, such as investigation, restricting use, proposing a threshold change or considering a previous revision; request absent decisions from the accountable owner. Do not automatically change a decision threshold, retrain, roll back or publish from a governance review.

## Review rule-release and alert-feedback evidence

Use this branch for offline governance of a rules-only release, alert-derived feedback, fulfillment evidence or a rule-impact comparison. Inspect the operational owner's revision-bound acceptance packet and disposition the supplied criteria, cases, results and decisions. Missing specifications or results become evidence requests to that owner, not a test-design or release-execution task. The enablement deck motivates the distinctions below but does not establish a current product state machine or API contract.

1. **Bind the release and its acceptance criteria.** Inspect the supplied release ID, candidate/baseline rule and strategy revisions, changed dependencies, intended decision/action scope, test population, accountable release owner and required approvers. Match each owner-approved criterion to the supplied case/result and revision; request absent criteria, cases or results from the operational owner. Link model validation only when a model or its use changes; otherwise mark model-only evidence not applicable with a reason. Assess rule testing, rule/strategy approval and deployment/activation as distinct gates, each with revision, timestamp and locator. A displayed lifecycle state or model approval does not establish any later gate. If deployment is not in scope or unobserved, bound the disposition to the named offline stage.
2. **Inspect feedback without collapsing event stages.** In the supplied alert/message records, check pseudonymous join IDs, event time, originating decision/rule revisions, selection/routing mechanism, analyst or automated disposition, marking/reason revision, marking timestamp, posting acknowledgment, downstream receipt, authoritative outcome source/time and reconciliation status. Distinguish a recorded marking, an asynchronously posted event, a consumed event and confirmed fraud/legitimate feedback; none alone proves all later stages. Record supplied unmatched, pending, duplicate, corrected and conflicting counts separately, and request missing stage evidence from its owner. A missing receipt is missing evidence, not a demonstrated delivery failure; an explicit rejection or contradicted criterion is failed evidence. Do not infer retry, ordering, deduplication or delivery guarantees from the deck.
3. **Assess feedback coverage before reuse.** Apply the population and label-maturity rules above rather than treating alert markings as a new ground-truth definition. Inspect counts from eligible events through alerted, assigned, reviewed and outcome-confirmed subsets, with selection/routing changes and exclusions. Identify how thresholding, prioritization, analyst availability, automatic disposition or customer response affects which outcomes become observable. Report unreviewed and unresolved subsets and whether non-alerted outcomes can be measured; request unavailable coverage evidence from the owner. If selection bias cannot be bounded, qualify generalization to the wider population; do not convert reviewed-alert precision into population recall or treat absent feedback as legitimacy.
4. **Assess the supplied impact comparison.** Check candidate and baseline rule/strategy revisions, evaluation mode (offline replay, scenario, or observed operation), synthetic versus other approved data status, cohort definition/size, event and outcome cutoffs, exclusions, overlap/multiple-rule attribution, metric numerator/denominator, label coverage and evidence locator. Reuse the metric definitions above. Establish from the records whether both ran on the same eligible events and dependencies; otherwise identify population, routing, volume and label-maturity differences and limit causal claims. Report supplied absolute counts alongside rates and distinguish flagged value, confirmed loss, recovery and actual intervention outcomes. Request absent comparison inputs/results from the operational owner; a dashboard title, demo report or screenshot without its underlying cohort/calculation evidence cannot establish improvement.
5. **Assess action authorization and fulfillment separately.** For proposed or observed contact, block/unblock, payment hold/release or list change, inspect action type, approving authority, approved scope, target revision, authorization locator, request time, downstream result/receipt, effective time and reconciliation evidence. Distinguish requested, acknowledged, applied, failed and unknown effects. A fraud marking or release recommendation is not authorization to contact a customer or change an account, payment or list; a successful post is not proof of fulfillment. For queued, timed-out or stand-in paths request evidence of the response actually used and the later outcome, without assuming product delivery behavior. Record missing authorization as a blocker for the proposed action and return reconciliation or execution work to the authorized operational owner; this review issues no live requests.

### Offline review result

Return one row per applicable gate (rule testing, release approval, deployment/activation if in scope, feedback reconciliation, population/label suitability, impact comparison, action authorization and fulfillment if in scope):

| Field | Required content |
| --- | --- |
| Scope | Gate, release/rule/model revisions and applicable cohort/window |
| Criterion | Supplied owner-approved acceptance condition, revision and accountable owner; request an absent criterion rather than constructing it |
| Evidence | Dated locator, supplied case/result, observed versus prepared/unrun stage, synthetic status where relevant and unresolved counts; reference protected records without copying customer data |
| Disposition | Evidenced, missing, failed, accepted exception, or not applicable with reason; missing includes unrun checks and absent required approvals, while failed requires an observed contradiction or explicit rejection |
| Exception record | Actual gap/failed result, authorized acceptor, decision locator/date, rationale, conditions and expiry/review date; an owner's exception does not turn the underlying result into a pass |
| Consequence | Readiness limitation, accountable operational/model owner, exact evidence or decision requested and next permitted owner action |

Conclude with offline readiness supported/not supported/undetermined for the specifically named next stage against the recorded criteria, separately identifying missing evidence and demonstrated failures. List approval and execution steps still outside this review. For a rules-only candidate with supplied model approval but no required rule-release approval, mark model-only development/selection checks not applicable with rationale and the rule-release approval gate missing; do not report release readiness supported or create tests to close that gap. An agent disposition is not owner acceptance, and model approval never substitutes for rule-release or customer-action authorization.
