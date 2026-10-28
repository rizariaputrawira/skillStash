# Findings and disposition

Load before delivering the review. Report what the inspected evidence establishes, what it leaves unknown and what decision is affected. Leave supplied artifacts unchanged.

## Ordered report

1. **Scope and evidence.** Intended use/decision, reviewed artifact names and revisions, source snapshot/as-of dates, population/grain, metric definitions, timezone/windows, authorized actions actually taken, and exclusions. List material unavailable evidence. Avoid reproducing sensitive rows.
2. **Prioritized findings.** Order by decision impact and urgency, not by discovery order. Use the fields below. Include material unsupported conclusions even when arithmetic cannot be refuted; do not dilute them into an unprioritized list of generic questions.
3. **Calculation coverage.** For each decision-driving metric state reported value, independent expected expression/value, units, precision/tolerance, evidence locator, assumptions and status. Separate local recalculation from checking supplied summaries, inspection only, and unverified. Give reason and needed evidence for unverified calculations. Never label a calculation verified because code or an output file exists.
4. **Disposition and next evidence.** State ready for the named use within inspected scope, not ready because of named blockers, or insufficient evidence for a readiness judgment. A material evidence gap can block reliance without proving a numeric error. List the minimum correction/evidence and re-review conditions. If no findings remain, say “No material issues found in inspected evidence” with coverage limits, not “correct” without qualification. State that supplied artifacts were left unchanged. This is not governance authorization, production release approval or compliance certification.

## Finding fields

- **ID, priority and title:** one issue per finding unless inseparable.
- **Location:** file/revision plus page/section, query expression/line, table/cell or figure/series. If no line numbers exist, use a stable quoted claim or other precise locator; do not invent lines.
- **Classification:** verified discrepancy; unsupported material claim; or unresolved evidence gap. State the observed evidence separately from its interpretation.
- **Evidence:** reported statement/value, supplied source locator and independent calculation or reasoning. Missing evidence is named, not fabricated. If a claim is contradicted only under assumptions, show those assumptions.
- **Impact:** concrete decision, affected population/metric and direction/magnitude when known. Do not invent exposure or monetary loss.
- **Correction/check:** the specific wording/definition/calculation change to consider, or the evidence/check that would discriminate explanations. Recommendations are not applied fixes.
- **Verification limit:** what was checked, what was not and what would close the issue.

## Priority calibration

Use labels that reflect the stated use, explaining the reason each time:

- **Blocking:** a demonstrated issue or missing evidence prevents reliance on a decision-driving conclusion for this use. A wrong weighted average used to set a per-user budget can qualify; an unsupported causal estimate driving a rollout can qualify even without a proven arithmetic defect.
- **Material:** changes interpretation or a consequential secondary result, but does not block every use of the work. State which uses remain supported.
- **Minor:** a local clarity/presentation/reproducibility issue with limited decision consequence in this scope. If the impact is unknown, say so rather than inventing it or downgrading automatically.

Do not escalate because a statistical threshold was crossed, or downgrade because evidence is missing. Severity and certainty are separate: a high-impact unsupported claim may warrant a blocking evidence request. Record open questions separately when there is not yet a concrete material claim at risk.

## Synthetic finding shape

**R1 — Blocking for per-user budget — Equal-group mean presented as per-user mean**

- Location: synthetic report, summary table, “All users / average spend” cell: 125.
- Classification/evidence: verified pooling discrepancy conditional on the supplied disjoint-group summaries and per-user definition. A: 100 users × 50; B: 10 × 200; expected 7,000 / 110 = 63.63636… currency units per user, not 125.
- Impact: using 125 to allocate an all-user budget overstates the specified mean; underlying source completeness remains unverified.
- Correction/check: replace the pooling calculation under the stated definition, reconcile group counts and membership, and re-review downstream budget values. No supplied artifact changed.
- Verification limit: arithmetic checked from synthetic summaries, not from source rows; actual runtime recalculation is not claimed.

These report fields and example are independently authored. The statistical source basis is recorded in [../SOURCES.md](../SOURCES.md).
