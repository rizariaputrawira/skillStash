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
