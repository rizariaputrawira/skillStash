# Metrics and time comparisons

Use for new or disputed metrics, weighted summaries, cohort/funnel analyses, and trends. Input: question contract and profiled evidence. Result: defined metrics with eligibility counts, reconciliation, and comparable windows.

## Define before calculating

1. Write a metric card: `name/version | business meaning | population/unit | source/snapshot | grain/keys | numerator | denominator/eligibility | units | inclusion/exclusion | missing/zero policy | time field/timezone/window | weighting | owner or unresolved definition`.
2. Distinguish event counts, unique entities, exposure time and stock snapshots. Specify whether refunds/cancellations revise prior periods or enter the current one. Define currency conversion and nominal/real treatment if material. Do not sum snapshots across dates as though they were flows.
3. For a proportion, the numerator must be a subset of the denominator at the same unit/window. A rate per exposure can exceed one and is not necessarily a proportion. Count each entity according to its defined eligibility, not the number of joined rows. Carry numerator and denominator into the result table.
4. Define zero and missing behavior. A zero denominator gives an undefined ratio, not zero or infinity as a business result. Unknown eligibility/outcome is not automatically failure. Report excluded/unknown counts and how they affect interpretation.

## Aggregate correctly

- A pooled proportion is `sum(successes) / sum(eligible)`, not an unweighted average of subgroup percentages. A mean of subgroup rates is valid only for the explicitly different estimand of an equally weighted subgroup.
- A weighted mean is `sum(w_i * x_i) / sum(w_i)` over the declared eligible, observed pairs. Explain weights, which rows entered both sums, and missing-value handling. Require a nonzero total weight; ordinary positive-weight summaries use nonnegative weights. Signed or survey weights need design-specific treatment rather than casual reuse of ordinary standard errors.
- Combine subgroup means using their relevant observed counts/weights, not a mean of means. Medians and quantiles cannot generally be pooled from subgroup medians/quantiles alone. Do not manufacture missing sufficient statistics.
- Absolute change is `new - old`. Percentage-point change for proportions is `100 * (new - old)`; relative percent change is `100 * (new - old) / old` for a meaningful positive baseline. With zero or negative baselines, show absolute change and an explicitly agreed alternative interpretation instead of a misleading growth percentage. More than 100% growth is possible.

Original synthetic arithmetic: segments with `1/2` and `9/90` successes pool to `10/92 = 10.87%`, not `(50% + 10%)/2 = 30%`. Values 10 and 20 with weights 2 and 8 have weighted mean `(20 + 160)/10 = 18`. With no eligible records the rate remains undefined, even if both count fields are zero.

## Reconcile competing numbers

1. Align definitions, extraction/as-of times, units, grain, filters, timezones, late-arrival policy and weighting before comparing values. Name each source/version; apparent disagreement can be different valid estimands.
2. Create a bridge from source to result: raw rows/entities → authorized eligibility filters → key/join effects → missing handling → aggregation. Show counts and relevant totals at each stage without leaking records.
3. Separate explained differences from residual differences. Use a tolerance justified by rounding, precision or the metric owner, not a universal acceptable discrepancy. A material unresolved residual prevents a definitive reconciled headline; retain both results with the specific evidence needed to settle them.
4. Independently check a control total and a contrasting subgroup; inspect whether changing group mix reverses the aggregate story. Report within-group and pooled results when composition matters rather than choosing the more favorable one.

## Cohorts and funnels

1. Fix entity identity, cohort-entry event, cohort membership rule and observation cutoff. Choose first entry or explicitly allow re-entry; do not change membership using future information. Separate acquisition cohorts from a rolling active population.
2. Align cohort age and follow-up opportunity. Define retention precisely: active in interval k, ever returned by k, and continuously retained are different metrics. Specify whether the denominator is the original eligible cohort or an explicitly defined risk set. Mark not-yet-observable cells as unavailable, not zero; disclose censoring and loss to follow-up.
3. For funnels, define ordered steps, entity/session unit, repeat-event handling, allowed elapsed time, and entry eligibility. Derive nested populations from valid sequences for an ordered closed funnel. Cross-sectional counts of step events are not a conversion funnel.
4. Show each step's eligible entity count and both requested rates: step-to-step `n_k/n_(k-1)` and entry-to-step `n_k/n_0`. State zero-denominator behavior. Under the same nested population, ordered funnel counts cannot increase; an increase prompts investigation of definitions or joins, not silent capping. Open funnels and re-entry need their own explicit denominators.
5. Compare cohorts/funnels only after aligning maturity, calendar effects, eligibility, instrumentation and channel mix. A conversion change does not identify which intervention caused it.

## Trends and incomplete periods

1. Use explicit window boundaries, preferably `[start, end)` with timezone and a source completeness watermark. A timestamp cutoff is not proof that late-arriving events are complete. Select fully observed periods or align elapsed windows with comparable ingestion lag.
2. Distinguish missing observations, observed zero, partial coverage and future periods. Address weekday/holiday mix, seasonality, changed population/exposure, measurement changes and relevant price effects. Normalize by exposure only when it answers the question and the exposure is measured comparably.
3. Label partial-period totals. Do not compare a month-to-date total against a whole month as a performance trend. If the matching historical window is unavailable, give the partial total and withhold the like-for-like claim. A run-rate projection requires an explicit model/assumptions and must remain separate from observed actuals.
4. Keep raw period results beside rolling/smoothed summaries; document alignment and window size. Do not infer independent daily observations or a causal change from a smoothed line.

### Original synthetic incomplete-period case

Question: “Did October revenue collapse compared with September?” Supplied aggregates are synthetic currency units, same population/definition, UTC, and no late arrivals within the stated complete window. The extraction watermark is **2026-10-11 00:00 UTC**, so only October 1–10 is complete.

| Event window, UTC `[start, end)` | Revenue | Coverage |
| --- | ---: | --- |
| 2026-09-01 to 2026-09-11 | 300 | Complete first 10 days |
| 2026-09-11 to 2026-10-01 | 600 | Complete remaining September days |
| 2026-10-01 to 2026-10-11 | 330 | Complete first 10 days; month incomplete |

September's full-month total is `300 + 600 = 900`. The naive `330/900 - 1 = -63.33%` compares unequal windows and cannot support the collapse claim. The aligned first-10-day comparison is `330/300 - 1 = +10%`, absolute change +30. Report that narrow observed comparison, not full-month October growth or a causal improvement. Calendar mix can still differ. Do not multiply October by a month-length factor without separately justified forecasting assumptions. Independent check: a source subtotal of September first 10 days plus remainder must reconcile to 900; the +30 difference divided by 300 is 10%.
