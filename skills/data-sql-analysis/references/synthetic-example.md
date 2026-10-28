# Synthetic join-inflation and denominator fixture

Load for a concrete analytical query or an independently calculable regression case. Original synthetic data; no credentials or personal data. **Target: PostgreSQL 17**, using documented `WITH`, `VALUES`, date literals, joins, aggregates and numeric casts. Documentation-backed, not runtime-verified during authoring; not a PROC SQL/FedSQL example or a cross-engine compatibility claim.

## Input and result contract

- Purpose: daily fulfillment fraction by request date, not shipment date. Each request is eligible once; all its supplied fulfillment events count. No late-arrival cutoff is modeled. Real data require a defined as-of cutoff and overfulfillment/correction policy.
- `requests`: one row per non-null unique integer `request_id`; `day` is a non-null reporting date already assigned by the source; `requested_units` is a non-null positive integer. No timestamp conversion; date labels refer to the agreed UTC reporting calendar.
- `fulfillments`: one row per non-null unique integer `event_id`, with non-null foreign key `request_id` and non-null nonnegative integer `delivered_units`. Multiple events per request are expected. In this fixture all keys match. Missing/unmatched keys violate the input contract and must be reported, not treated as zero fulfillment.
- Population: request dates `[2026-10-01, 2026-10-04)`. All fixture rows qualify. Calendar covers every day of that interval, including no requests. No other filters/categories.
- Output, ordered and unique by `day`: `day` (date, non-null), `requested_units` and `delivered_units` (numeric, non-null totals), `fulfillment_fraction` (numeric, nullable). Fraction is pooled delivered/requested units, not mean request-level fractions. Zero requested units means null fraction; absence of events for a valid request means zero delivered units in this particular contract.
- Preparation-only by default. If separately authorized to execute, allowed reads are only the literal relations in this statement; no connection/setup/write is implied. Proposed fixture budget: one submission, three output rows, at most one second server execution using an existing approved control. If that control/engine is unavailable, deliver the query unexecuted; do not install a substitute engine or silently weaken the budget.

## Query

```sql
WITH calendar(day) AS (
    VALUES (DATE '2026-10-01'), (DATE '2026-10-02'), (DATE '2026-10-03')
),
requests(request_id, day, requested_units) AS (
    VALUES (101, DATE '2026-10-01', 10),
           (102, DATE '2026-10-01', 90),
           (103, DATE '2026-10-02', 20)
),
fulfillments(event_id, request_id, delivered_units) AS (
    VALUES (1, 101, 4), (2, 101, 6), (3, 102, 45)
),
fulfillment_by_request AS (
    SELECT request_id, SUM(delivered_units) AS delivered_units
    FROM fulfillments
    GROUP BY request_id
),
daily AS (
    SELECT r.day,
           SUM(r.requested_units)::numeric AS requested_units,
           SUM(COALESCE(f.delivered_units, 0))::numeric AS delivered_units
    FROM requests AS r
    LEFT JOIN fulfillment_by_request AS f
      ON f.request_id = r.request_id
    WHERE r.day >= DATE '2026-10-01'
      AND r.day < DATE '2026-10-04'
    GROUP BY r.day
)
SELECT c.day,
       COALESCE(d.requested_units, 0::numeric) AS requested_units,
       COALESCE(d.delivered_units, 0::numeric) AS delivered_units,
       d.delivered_units / NULLIF(d.requested_units, 0::numeric)
           AS fulfillment_fraction
FROM calendar AS c
LEFT JOIN daily AS d ON d.day = c.day
ORDER BY c.day;
```

The child preaggregation produces one row per request key before the parent join, so request 101 contributes its denominator only once. It is justified here because delivered units are additive and no event-level attributes are needed downstream. The bounded calendar then preserves a no-request day. This is a correctness decomposition, not a claim of faster execution or guaranteed CTE materialization.

## Independent expected result

Manually enumerate requests, without the raw joined rows: request 101 has `(requested, delivered) = (10, 4 + 6)`, request 102 `(90, 45)`, request 103 `(20, 0)`.

| day | requested_units | delivered_units | fulfillment_fraction |
| --- | ---: | ---: | ---: |
| 2026-10-01 | 100 | 55 | 0.55 |
| 2026-10-02 | 20 | 0 | 0 |
| 2026-10-03 | 0 | 0 | null |

Formatting/trailing zeros may vary. Compare numeric values, not display strings. The whole-interval pooled fraction is `55 / 120 = 11 / 24`, not the sum/mean of daily fractions.

**Two wrong answers to detect:** a raw parent-to-event join repeats request 101, giving October 1 requested units `10 + 10 + 90 = 110` and fraction `55 / 110 = 0.5`. Averaging request-level fractions gives `(10/10 + 45/90)/2 = 0.75`, not the pooled `0.55`. `SUM(DISTINCT requested_units)` is not a fix: two different valid requests can have the same requested units.

## Validation output contract

This table is the pre-execution validation output, not an observed run. An authorized consumer replaces the observed/status fields with evidence from actual checks. Check source keys and unmatched rows before trusting the result, not just the final totals.

| Check | Expected | Observed | Status |
| --- | --- | --- | --- |
| Input request/event key duplicates or null keys | 0 / 0 | not run | unverified |
| Non-null unmatched fulfillment request keys | 0 | not run | unverified |
| Source request rows / fulfillment rows | 3 / 3 | not run | unverified |
| Raw left-join rows / preaggregated left-join rows | 4 / 3 | not run | unverified |
| Output schema / unique days / complete days | four columns as above / 3 / all three calendar dates | not run | unverified |
| October 1 requested / delivered / fraction | 100 / 55 / 0.55 | not run | unverified |
| October 2 and October 3 fractions | 0 / null | not run | unverified |
| Independent interval requested / delivered | 120 / 55 | not run | unverified |

For an authorized exercise, obtain actual output types from result metadata, count duplicate output days, compare ordered rows to the manual table, and reconcile totals to the separately enumerated request/event relations. Use exact comparison for integers/finite decimals here; use a declared tolerance for the repeating interval fraction. Preserve statement identity and engine version with observed evidence. No plan or speed result is supplied.

## Adversarial variations

- Add request `(104, DATE '2026-10-01', 10)` with no events: expected day-one denominator `110`, numerator `55`, fraction `0.5`. Legitimate equal amounts must both count; this exposes `SUM(DISTINCT ...)`.
- Add an event with a null request key or unmatched `999`: input checks must fail/report the respective null or orphan count. Do not silently interpret it as a valid request's zero event count, and do not publish reconciled source totals until its disposition is agreed.
- Duplicate request 101: uniqueness fails before metric validation; do not hide it with `DISTINCT`. Remove all fulfillments: all eligible request days have zero delivered units, no-request day remains null fraction. A missing/unloaded fulfillment source is not equivalent to this confirmed-empty fixture.
- A requested zero-unit request changes the input contract; first agree whether it is ineligible or a zero denominator with null result. Do not infer that policy from `NULLIF` alone.

Technical source basis: [PostgreSQL 17 SELECT](https://www.postgresql.org/docs/17/sql-select.html), [aggregates](https://www.postgresql.org/docs/17/functions-aggregate.html), [conditional expressions](https://www.postgresql.org/docs/17/functions-conditional.html). Full consultation and rights record: [SOURCES.md](../SOURCES.md).
