---
name: data-analysis
description: "Analyze supplied data or results to answer business questions, reconcile metrics, compare cohorts or trends, select statistical methods, and communicate evidence."
---

# Data analysis

For an analyst turning a question into a defensible finding from supplied files, tables, SQL/pandas/SAS results, or offline evidence. Own the analysis, not general ML training, database-pipeline engineering, or service/session execution.

## Authority and evidence

Selection grants no installation, authentication, live access, execution, write, publication, or deployment authority. Work within the assignment's permitted sources, tools, resource bounds, and output destination. Use supplied evidence when execution is unavailable; label computations proposed, supplied, recomputed, or independently checked rather than implying a run occurred. Do not expose credentials, personal records, or restricted small groups in outputs.

Choosing SAS does not authorize contact with SAS or establish syntax compatibility. Keep analytical procedure version-neutral; any SAS syntax, option, capability, or support claim requires the exact product/release and owning official documentation. Unknown compatibility is not unsupported compatibility. Leave sessions/submission, CAS action/table lifetime, REST jobs, registration/publishing, and lifecycle governance with their execution owners; this workflow remains usable without them.

## Workflow

1. **Bind the question.** Record the decision/audience, requested estimate or comparison, target population, observation grain and keys, metric meaning/units, dates/timezone, complete-window rule, source lineage and snapshot/as-of time, permitted access/output, and delivery format. Separate event time from ingestion time. Resolve material ambiguities from supplied evidence; otherwise expose the missing fact, use clearly labeled alternatives where useful, and withhold the affected conclusion.
2. **Establish usable evidence.** Inventory actual inputs, schemas, coverage and existing query/code/log provenance. Before profiling raw data, uncertain types/keys, or joins, read [profiling and evidence](references/profiling-and-evidence.md). Produce a bounded profile and issue ledger; distinguish sampled checks from full-population checks. Preserve source data and make every analytical exclusion or transformation explicit.
3. **Define and reconcile the calculation.** Write the metric contract before calculating. For metric disagreements, ratios/weights, cohorts, funnels, or time comparisons, read [metrics and time comparisons](references/metrics-and-time.md). Finish with numerator/denominator counts, eligible coverage, reconciliation evidence, and like-for-like windows; do not silently treat partial periods as complete.
4. **Choose the evidential strength.** Start with descriptive results. If estimating beyond the observed data, testing a difference, evaluating uncertainty, or interpreting an association, read [methods and uncertainty](references/methods-and-uncertainty.md) before selecting a method. State the estimand, design, assumptions, dependence, method and limitations. An observed association or before/after change alone is not a causal effect.
5. **Check the result independently.** Reconcile totals across transformations, recompute key results with a distinct calculation or source control total, and inspect boundary cases. Rerunning the same expression is not an independent check. Identify exactly what was checked and with which evidence; if necessary evidence or execution authority is missing, return a provisional result with an explicit unchecked scope rather than a verified claim.
6. **Deliver decision-ready evidence.** For charts or stakeholder deliverables, read [visualization and reporting](references/visualization-and-reporting.md). Match every headline to a checked table cell, qualify coverage/uncertainty, and separate findings, hypotheses and recommendations. Finish only when the output contract below is met or its specific blocked fields are stated.

## Output contract

Return these sections in the requested medium; a small analysis can use a compact table and short notes:

- **Question and scope:** decision, population/grain, metric definitions, windows/timezone, source snapshot and allowed destination.
- **Findings:** answer with values, units, sample/eligible counts, numerator/denominator, comparison basis, and uncertainty where justified; say when the evidence cannot answer.
- **Quality and reconciliation:** material issues, transformation/exclusion counts, independent checks and their outcomes; supplied versus recomputed versus unchecked evidence.
- **Interpretation and limits:** assumptions, selection/missingness/coverage/dependence concerns, observational versus causal status, and plausible alternative explanations.
- **Reproduction and next decision:** source/query/result identifiers, parameters, tool versions where used, ordered transformations or authorized analysis code, chart/table inputs, and a bounded recommendation or precise missing prerequisite. Record random seeds/resampling design if relevant. Provide reproducibility details in the response when file writes are not authorized.

Consult [source and rights records](SOURCES.md) when a technical claim needs its documentation basis; consulted documentation is not an environment compatibility guarantee.
