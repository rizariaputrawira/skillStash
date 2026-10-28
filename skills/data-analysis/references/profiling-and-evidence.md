# Profiling and evidence

Use when ingesting supplied datasets, evaluating quality, or joining evidence. Input: the question contract, authorized artifacts and metadata. Result: a reproducible profile, lineage record, and dispositioned issue ledger, not an automatically cleaned replacement dataset.

## Establish the evidence boundary

1. Identify each artifact/query/result and snapshot/as-of time; record file format, encoding, schema, producer, extraction filters, and known row/size limits. For supplied aggregates, determine what was already filtered or grouped; do not imply access to underlying records.
2. Limit inspection to necessary columns, time ranges and resource bounds. Begin with metadata and a bounded preview, then required aggregates. Use chunking or authorized source-side aggregation if needed; do not load an unbounded dataset merely to inspect it. A head preview is not a representative sample. Record any sample selection, size, seed and coverage; exact global uniqueness or rare-error claims require suitable full-data evidence.
3. Preserve the original artifact. Derive an analytical view with explicit transformations; do not overwrite sources. Check identifier strings/leading zeros, delimiters, decimal conventions, units, categorical codes, date parsing, timezone and missing sentinels against metadata. Reject or quarantine unparseable values with counts and reasons rather than silently coercing them away. A timezone-naive timestamp with no source convention does not justify an invented timezone.

## Profile at the declared grain

Produce a compact table with field, expected meaning/type, observed type, nonmissing/missing counts, distinct count or labeled estimate, relevant range/quantiles, and flagged issues. Include:

- Rows, distinct entities, expected key uniqueness, null key counts and duplicate-key groups. Separate repeated events or legitimate line items from duplicated records; identical rows can still be legitimate without a business key.
- For numbers: finite/nonfinite counts, units, quantiles, extrema and subgroup/time distributions; compare mean and median when tails matter. Negative revenue can represent refunds; a large increase can be genuine. Use documented domain constraints, not universal business thresholds.
- For categories: frequencies including missing/unknown/unmapped categories; rare labels, inconsistent casing/whitespace and code drift. Do not silently merge labels.
- For dates: earliest/latest event and ingestion times, expected calendar coverage, gaps, late arrivals, duplicates, and complete-window status. Distinguish zero activity from unavailable observations.
- Missingness by relevant period/group and joint missingness of outcome/exposure/key fields. Report the denominator for each missingness rate. Empty, not applicable, suppressed and unknown can require different meanings.

## Investigate, do not automatically erase

For every material anomaly record `issue | evidence | affected rows/metric | status | proposed treatment | authority | sensitivity/result impact`. Status should distinguish confirmed source error, plausible genuine value and unresolved concern.

Trace suspicious records to provenance and domain rules. Use distributions/plots to flag potential outliers, not to prove corruption. Preserve plausible extremes; consider robust summaries and a clearly labeled sensitivity analysis. Apply correction, exclusion, imputation, winsorization or deduplication only with an explicit justified analysis rule within the assignment's authority. Report before/after counts and impact. If the rule is unsettled, retain records in a flagged view and report alternatives or block the dependent result. A high missingness percentage alone does not determine whether a field is unusable.

## Guard every join or transformation

1. Declare join keys, grain on each side, intended cardinality, join type, null-key policy and expected unmatched handling. Test key multiplicities before joining. Repeated keys on both sides can multiply rows; preaggregate only when it preserves the metric definition.
2. Compare before/after row counts, distinct entities, matched/unmatched key counts and relevant control totals. Preserve unmatched populations long enough to explain losses. Report planned versus accidental row expansion separately.
3. Reconcile a small hand-traced example and a control total using a distinct route. A total agreeing after two wrong transformations does not prove every subgroup is right.

### If using pandas

These are documentation-bound observations from **pandas 2.3.3**, not guarantees about an installed version. Check the actual environment before authoring executable API-specific code.

- CSV parsing can interpret strings as missing and infer types; select `dtype`, `na_values`, `keep_default_na`, encoding and date conventions deliberately. Preserve identifier meaning; do not skip bad lines as a quality fix.
- Use missing-value predicates rather than equality to a missing sentinel. Keep row counts distinct from nonmissing field counts.
- `merge` can match null keys to null keys, unlike usual SQL equality-join behavior. Implement the declared null policy explicitly. Its `validate` parameter checks supported cardinalities; `many_to_many` does not check uniqueness. An `indicator` can expose match status.
- `DataFrame.groupby` defaults to dropping missing group keys in 2.3.3; make inclusion/exclusion explicit with `dropna`. Set categorical `observed` semantics deliberately rather than assuming absent categories represent observed zeros. Aggregations can exclude missing values; carry counts and check all-missing groups so missing does not become an unexplained zero.

If only SQL or SAS output is supplied, request or inspect row counts, filter/group definitions and missing handling already present in that evidence. Do not infer pandas semantics, SQL dialect behavior, or SAS syntax from one another.

Finish with issue disposition and the largest defensible analytical population. State which questions remain answerable even when a specific field or join is blocked.
