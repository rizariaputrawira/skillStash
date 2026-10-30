# Fraud reporting and analytical handoff

## Applicability

For building a fraud operations/strategy report, preparing an engine-model handoff, or using an available Copilot assistant. Report UI basis: supplied *SAS Visual Analytics: Designing Reports*, **2026.04–2026.05**, printed pp.7–10, 29–32, 46–49; SFD data constraints: *SAS Fraud Decisioning: User’s Guide*, **2026.03**, pp.197–199. These separate product editions do not establish a jointly tested installation. [SOURCES.md](../SOURCES.md) records identities and limitations.

When the report task includes templates/layout/themes, state/recovery, printing/summaries, distribution/sharing/embedding, packages/localization/settings, URL mappings or promotion, read [report operations](report-operations.md). These procedures supplement the fraud metric workflow; their source edition and visual-review limits remain distinct from live UI verification.

## Build a fraud report in Visual Analytics

1. Obtain authorized reporting data already available in CAS, its grain/keys, source revisions, refresh time and allowed audience. Agree metric definitions before selecting a chart: eligible transaction count, distinct alerted transactions, alert count, reviewed messages and confirmed outcomes are different quantities. Preserve currency and outcome-maturity windows. Do not infer a ready-made report dataset from the deck's demo titles.
2. In Visual Analytics, use **New report**. In **Data**, choose **Add data**, select the prepared source in **Choose Data**, then **Add**. Confirm the source is the intended table/snapshot, not an identically named table in another caslib. If data is absent, prepare the loading/access request; selecting a skill does not authorize importing transaction data.
3. Add a table or graph from **Objects** to the canvas, then assign the required data items from **Data**. Use **Options** for object presentation and verify data role assignments and aggregation. Build a detail table alongside the requested aggregate during verification so count inflation and filtering can be reconciled; do not expose protected identifiers to unauthorized viewers.
4. For a strategy-impact page, compare incumbent and candidate on a matched event population, dependency/state basis and label cutoff. For an operations page, show queue/workload age and review/feedback completeness separately from mature fraud outcomes. Define each rate's numerator and denominator; do not average subgroup percentages without weighting their denominators.
5. Add pages as needed using **New Page** in **Outline**. Apply the intended filters and inspect the same cohort in each relevant object. If a live page is required, configure **Periodically reload page data** under page options deliberately; without it, the guide says a page does not automatically update unless a query is initiated. A refreshed chart still depends on source-data freshness.
6. Use **Save** or **Save as** into the authorized folder, then **View report**. Reopen and inspect as the intended viewer where authorized: default filters, page navigation, totals, absent data and access restrictions must still be correct. Sharing or scheduled distribution needs its own audience/data authority. The guide warns that **Save as** does not copy existing report distributions and copies display-rule alert conditions without their recipients; do not assume a copied report preserves delivery.
7. Return report identity/location, source and refresh lineage, metric contracts, filters/aggregation, reconciled keyed counts, viewer checks and unperformed layers. Report only observed values; no benefit, prevented loss or causal improvement follows from a chart alone.

### Synthetic calculation check

Given 1,000 eligible transactions, 100 alerted transactions, 20 reviewed and 8 confirmed fraud with 12 unresolved: alert rate is 10%, review coverage among alerts is 20%, and the currently confirmed share of reviewed cases is 40%. That 40% is neither population recall nor established mature-label precision. Non-alerted and unresolved outcomes are missing. A rule that fires twice on one transaction must not double the distinct-alerted-transaction denominator.

Check field availability before designing historical views: the consulted SFD output-filter feature removes profile data by default and does not retroactively backfill later-added profile fields. Inspect the actual topic/filter/table contracts; a report cannot reconstruct evidence that was never retained.

## Prepare a model for the fraud strategy

This branch prepares a handoff, not a full model-training manual or universal deployment package. Record the actual engine-supported language/package, model/artifact identity, input schema, entity key, feature availability and profile read/update order. Distinguish a profile before the current message from one containing it. Preserve model score event, direction, units/scale and missing/error behavior; a score on a 0–900 scale is not a probability simply because it can be divided by 900.

Inventory preprocessing and ensure every transformation has exactly one owner across producer, model artifact and consumer. Before accepting trimming/export/packaging, compare keyed feature states, numeric scores and consuming decisions on missing/unseen values and threshold boundaries with predefined tolerances. If no scorer/runtime is available, deliver proposed cases and mark parity unperformed. Use the supported model association and deployment procedure from [rules and runtime](rules-testing-deployment.md), not assumed MAS equivalence. Training, registration, engine acceptance and strategy approval remain separate outcomes; no model-development success approves the whole SFD strategy.

## Use Copilot only where actually available

The report-design manual describes Visual Analytics Copilot as administrator-enabled, not enabled by default, with credential setup required before enablement. Do not configure credentials, change licensing or enable it merely to write a report. Where already enabled and authorized, request a bounded draft/report change using approved data, inspect the proposed/generated result, and verify source selection, fields, aggregation, filters and numbers exactly as for manual authoring. Never paste customer data or secrets into an unapproved assistant.

This is evidence for **Visual Analytics** Copilot at the consulted edition, not every SFD Copilot capability. The current official SFD product page separately describes assisted rule authoring, but supplies no release-specific UI contract. The deck's planned concepts are not a how-to for ETL, log analysis, model deployment or rule tuning. Obtain the exact target's documentation before those operations; the Agentic AI Accelerator is a different product surface.
