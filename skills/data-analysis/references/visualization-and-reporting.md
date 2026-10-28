# Visualization and reporting

Use when producing charts, a stakeholder summary, or an evidence handoff. Input: reconciled analytical tables and their checks/limitations. Result: understandable findings traceable to evidence; rendering or publication occurs only when authorized.

## Choose the display from the question

1. Identify the single comparison each display should support and its audience. Use the simplest encoding that preserves the needed information:
   - Ranked categories or a few comparisons: aligned bars or dots; preserve meaningful category order.
   - Time: a line for ordered development, columns for a few period totals, small multiples for many series. Keep actual time spacing; mark partial periods and breaks.
   - Distribution: histogram with documented bins, box plot with sample sizes, or points for small groups. A box-plot outlier marker is not proof of a source error. Show tails when relevant.
   - Association: scatterplot with units and coverage; manage overplotting without hiding observations. A fitted line is not causal evidence.
   - Cohort maturity or ordered funnel: a labeled matrix/table or bars with eligible counts, aligned ages and explicit denominator definitions. Mark unobservable cells differently from observed zero.
   - Estimates/uncertainty: dot-and-interval display with the interval type/level labeled. Confidence bars are not raw-data ranges.
2. Use a table when precise values, denominators, missing cells or many definitions matter more than visual shape. A chart is not required to make an analysis complete. For text-only or no-rendering contexts, return the checked table plus a chart specification; do not claim a rendered inspection.

## Construct without changing the meaning

1. Bind the display directly to the checked result table; document any additional grouping, binning, normalization or smoothing. Reconcile plotted totals/counts with that table. Do not calculate a second hidden metric only in plotting code.
2. Label title, population, units, date window/timezone, source/as-of time, denominator where material, uncertainty method and completeness. Distinguish actuals, partial periods, estimates and projections in words and encoding. A title should not state a stronger conclusion than the evidence supports.
3. Use a zero baseline for bars when length represents magnitude. A narrowed line/dot axis can be appropriate but must be explicit and not exaggerate the story; disclose log scales and verify they are valid for the displayed values. Avoid dual-axis comparisons unless their meaning and necessity are clear. Shared-axis small multiples make direct comparisons easier; label any exceptions.
4. Preserve gaps rather than drawing through missing data as if observed. Do not smooth across instrumentation changes without disclosure. Use consistent scales/units for comparable panels. Include a reference/target only when its source and meaning are known.
5. Do not rely on color alone: use labels, shapes or line styles. Choose readable contrast, legible text, and an appropriate sequential/diverging/categorical palette. Provide a concise text description and accessible table of the displayed values, subject to disclosure restrictions.
6. Respect allowed outputs: use aggregates or synthetic examples where needed, suppress restricted cells according to the governing policy, and check whether totals or other cells expose suppressed values by subtraction. Do not invent a universal small-cell threshold.
7. If a rendering tool is authorized, inspect the actual output for clipping, unreadable labels, misleading scales and broken legends at the intended size. Otherwise state rendering unverified. Tool availability does not authorize uploading data to a chart service or publishing externally.

## Write the decision note

Deliver in this order, keeping detail proportional to the question:

1. **Answer:** a qualified direct response and its decision relevance. If unresolved, say exactly which question the evidence cannot settle.
2. **Evidence:** the key checked figures with absolute/relative differences, units, denominators and comparable periods; attach a compact table/chart or exact result identifiers. Name the independent check rather than saying only “validated.”
3. **Interpretation:** distinguish observation from hypothesis and causal explanation. Relate effect size and uncertainty to a supplied decision threshold, or avoid declaring practical importance when none exists.
4. **Limits:** quality, missingness, selection, dependence, partial coverage and definition alternatives that could change the conclusion; separate these from cosmetic caveats. State whether results were supplied, recomputed, independently checked or remain provisional.
5. **Next action:** a bounded recommendation, its trade-off and needed evidence/owner. Do not authorize intervention, write-back or deployment by reporting it.
6. **Reproduction:** artifact/snapshot identifiers, query/analysis parameters and versions actually used, transformations/exclusions, independent-check method/results, and the exact display-input table. Supply these in the response if writing files is outside scope; do not include credentials or identifying records.

For the synthetic incomplete-period case in [metrics and time comparisons](metrics-and-time.md), a suitable headline is “October first-10-day revenue is 10% above September's matched window; October's full month is not observed.” Display 300 versus 330 with the complete UTC windows, not a chart of 900 versus 330 titled “Revenue collapsed.” Retain the full-month September total only as distinctly labeled context. The +10% comparison alone does not establish a causal improvement or forecast October's close.
