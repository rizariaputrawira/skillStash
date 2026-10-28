---
name: data-analysis-review
description: Review a supplied analysis, query, report or chart against its source evidence before relying on its conclusions.
---

# Review supplied analysis

For an agent reviewing existing analytical work, return evidence-backed findings and a bounded readiness judgment, not a replacement analysis. This is read-only review: preserve supplied artifacts. Selection grants no authority to install, authenticate, execute code, query production, write files, change data or fix findings. Independently recalculating supplied approved data locally is allowed only when the task authorizes it; otherwise show an inspectable calculation or request the missing evidence. Do not expose credentials or personal data in findings.

## Workflow

1. **Bind the review.** Identify the decision, audience, supplied report/query/chart revisions, source evidence and authorized inspection/recalculation scope. Record population, observational unit and output grain; metric numerator, denominator, units and weights; timezone, period boundaries, comparison windows, exclusions, refresh/as-of date and revision policy. Mark unresolved definitions explicitly rather than choosing convenient defaults. Finish with an evidence inventory and material questions.
2. **Trace claims to sources.** Map each decision-driving claim to a report section, query expression, chart series or table cell and to the source snapshot, filters, transformations and aggregation that produced it. Inspect what is supplied; request missing lineage rather than silently querying a system. When calculations, joins or period comparisons support a claim, read [calculation checks](references/calculations.md). Derive expected results independently from the definition, not by repeating the implementation. Record which results were actually checked, which only inspected and which remain unverified.
3. **Challenge interpretation.** For comparisons, tests, forecasts or explanatory/causal claims, read [bias and inference checks](references/bias-and-inference.md). Inspect population changes, dependent observations, time ordering, uncertainty and alternate explanations. Missing evidence can make a material claim unsupported; it is not proof the opposite is true.
4. **Review communication and reproducibility.** When charts, tables or reproduction evidence are supplied, read [presentation and reproducibility checks](references/presentation-and-reproducibility.md). Reconcile visible numbers with the underlying metric and determine whether the supplied evidence permits an independent reproduction. Artifact existence alone is not a pass.
5. **Deliver prioritized findings.** Read [findings and disposition](references/findings-format.md) before reporting. Give locatable evidence, concrete decision impact and a correction or discriminating check for every finding. Separate verified discrepancies from unsupported claims and open questions. State ready, not ready or insufficient evidence only for the inspected scope and intended use, naming any blocking evidence gaps. Return artifacts unchanged; no model-governance approval, compliance guarantee or claim of complete correctness.

## Completion

The report contains scope/revisions, evidence coverage, prioritized findings, verified/unverified calculations, unresolved material evidence and the bounded disposition. If a source or permission is absent, finish all other reachable inspection and name exactly what prevents the dependent conclusion. A clean review means no material issue found in inspected evidence, not proof that unseen data or processes are correct.

Source basis and rights boundaries: [SOURCES.md](SOURCES.md).
