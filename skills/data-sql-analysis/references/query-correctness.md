# Query correctness checks

Load for joins, aggregates, complete periods or windows. Apply to the bound engine/schema; the linked technical semantics below are PostgreSQL 17, not a universal SQL contract.

## 1. Establish keys and joins

- Record each input's one-row meaning, key columns, nullability and observed/declared uniqueness. An identifier's name is not proof of uniqueness; effective-dated dimensions require an as-of condition and nonoverlapping validity intervals, not simply an ID join.
- For each join record left/right grain, key predicates, expected `1:1`, `many:1` or intentional `many:many`, retained population and unmatched policy. A `1:many` match repeats left measures. Two children joined through a parent can multiply each other. Preaggregate children to the intended joining grain when the metric is additive there; preserve needed filter/attribution detail first. For existence-only eligibility, consider an evidenced dialect's `EXISTS` instead of introducing child rows.
- Check duplicate join keys (`GROUP BY` the full key, `HAVING COUNT(*) > 1`), null components, match multiplicities and unmatched rows on both sides within the same population/snapshot. Define expected counts, not just query text. If checks exceed allowed cost, label cardinality unverified and do not assert validated totals.
- In PostgreSQL 17 equality involving null yields unknown; null keys therefore do not match through ordinary `=`. Test with `IS NULL`, not `= NULL`. Do not replace missing IDs with a shared sentinel or use null-safe equality without a business rule: either can merge unrelated unknown entities. Report null keys separately from non-null unmatched keys. Empty strings, whitespace, case and SAS missing values require target-specific rules; do not silently normalize keys.
- Preserve outer-join intent. Filtering a right-hand column in `WHERE` can remove unmatched left rows; place eligibility in the input or `ON` when unmatched left rows must remain. Verify retained population explicitly. Use explicit join predicates rather than schema-dependent `NATURAL JOIN`.
- Reconcile row counts and additive source totals before/after each grain change. A legitimate detail query may expand rows; its parent totals must not be summed repeatedly. `DISTINCT` is appropriate only when deduplication itself has a defined business meaning and tie-resolution rule, not as a repair for an unexplained join.

PostgreSQL 17 sources: [joins and filter placement](https://www.postgresql.org/docs/17/queries-table-expressions.html), [null comparisons](https://www.postgresql.org/docs/17/functions-comparison.html).

## 2. Define aggregates and rates

- State whether counts mean rows, non-null observations or distinct entities at a defined grain. After a left join, `COUNT(*)` includes the unmatched placeholder row; count a guaranteed non-null right key when counting actual matches.
- Distinguish no activity, unknown measurement and true zero. PostgreSQL 17 `COUNT(column)` ignores nulls, `SUM`/`AVG` operate on non-null inputs and `SUM` of no input rows is null. Track missing counts and apply `COALESCE` only where the contract makes absence zero; do not turn unreported amounts into zero by habit.
- For a pooled rate, sum eligible numerators and eligible denominators at the same population/grain, then divide in a non-integer numeric type. For a weighted mean of values, specify `SUM(weight * value) / SUM(weight)` over rows with both valid values and valid weights. An unweighted average of subgroup rates answers a different question. Define allowed weights, overlap/double-counting rules and rounding at the reporting boundary.
- Handle zero denominator explicitly, often as null plus a reason/status. Do not claim zero or infinity by default. Check negative denominators, numerator eligibility and out-of-range rates against the metric's actual meaning; not every rate is a probability or bounded by 100%.
- Aggregate before joining only if it preserves metric semantics. Distinct-person counts, medians and overlapping cohorts usually cannot be added across partitions. Preserve numerator/denominator components when later recombination is required.

PostgreSQL 17 sources: [aggregates](https://www.postgresql.org/docs/17/functions-aggregate.html), [COALESCE and NULLIF](https://www.postgresql.org/docs/17/functions-conditional.html).

## 3. Complete time and order-dependent calculations

- Bind timestamp type, source timezone, reporting timezone, date boundaries, calendar/fiscal rules and daylight-saving treatment before bucketing. Prefer `[start, end)` boundaries for adjacent periods. Verify dialect-specific timestamp conversion against matched documentation; no conversion syntax is supplied here.
- Build or use an authorized complete calendar at reporting grain, restrict it to the requested interval and left join period aggregates. If all categories must appear, define their eligible population and bounded calendar/category combinations; do not accidentally create an enormous Cartesian grid. Distinguish a zero-activity period from absent/unloaded data. Preserve coverage/late-arrival evidence.
- For a previous-day/month comparison, complete the relevant calendar first or use an explicit date match. `LAG` over sparse rows means previous available row, not necessarily previous calendar period. Do not compute growth from a zero baseline without the agreed result policy.
- For supported windows, define partition, ordering, ties, null order and frame. Use a unique stable tie-breaker for `ROW_NUMBER`, top-one or event sequencing if exactly one row must be chosen. If ties are business peers, use rank semantics without destroying that peer grouping, then give presentation a stable order separately. If no meaningful tie-breaker exists, report the ambiguity rather than inventing which record is latest.
- Define `ROWS`/`RANGE` and frame boundaries explicitly for cumulative/rolling values; default peer frames can surprise. PostgreSQL 17 `LAST_VALUE` uses the frame, not automatically the entire partition. Window ordering does not replace a final result `ORDER BY`.

PostgreSQL 17 sources: [window functions and frames](https://www.postgresql.org/docs/17/functions-window.html), [SELECT and result ordering](https://www.postgresql.org/docs/17/sql-select.html).

## 4. Independent evidence

1. Check names, types, nullability expectations, units, unique output keys, row count and complete-period coverage against the result contract.
2. Calculate a small source-grain case by hand or an independently structured calculation, including one-to-many children, unequal denominators, zero activity, missing values and unmatched keys. Specify expected values before comparing observed results. Do not use the same inflated join as the independent total.
3. Reconcile numerator/denominator totals, match counts and exclusions to the eligible input population at the same snapshot/cutoff. If data change between checks, reconcile the snapshot difference before diagnosing the SQL.
4. Record expected versus observed values, tolerance/rounding, source/statement identity and status. A sample supports the sampled cases, not full-table uniqueness or production performance. Failed checks require correction or an explicit unresolved discrepancy; never silently discard troublesome rows.
