# DATA step and PROC SQL semantics

## Applies-to and owning sources

Language claims here use [SAS 9.4 Language Reference: Concepts, Sixth Edition](https://documentation.sas.com/api/docsets/lrcon/9.4/content/lrcon.pdf), displayed **April 5, 2023**, chapters 4–9, 20–23; [MERGE Statement](https://documentation.sas.com/doc/en/pgmsascdc/9.4_3.5/lestmtsref/n1i8w2bwu1fn5kn1gpxj18xttbb0.htm), resolved **9.4_3.5** collection; and [SAS 9.4 SQL Procedure User's Guide, Fourth Edition](https://go.documentation.sas.com/api/collections/pgmsascdc/9.4_3.5/docsets/sqlproc/content/sqlproc.pdf?locale=en), displayed **October 8, 2026**, chapters 2–3 and summary-function reference. Consulted **2026-10-28**. [Sources and limits](../SOURCES.md).

These own Base SAS semantics, not a universal compatibility declaration. The example is a **Compute/Base DATA step and PROC SQL illustration**, conditioned on target-matched documentation and authorization via [runtime binding](runtime-binding.md). CAS execution, FedSQL, DS2 and SQL delegated to a database are not covered by the fixture. Exact maintenance-level support and equivalence on an unidentified Compute target are **not established**. Known evidenced semantic differences below compare language/runtime constructs; no release-change history is established. Target check: confirm the actual execution engine and documented constructs, then inspect actual log and output if execution is authorized.

## Bind values before transforming

1. Inspect schema and representative safe metadata before writing expressions. Separate character identifiers from numeric measurements: converting an identifier can lose leading zeros. In the consulted Base SAS edition, variables are character or numeric; numeric storage also represents dates/times. Declare intended character type/length before the first defining use, because first occurrence can determine attributes. Check bytes and encoding, not just visible character count; reconcile truncation at concatenation, import and engine boundaries.
2. Distinguish stored values from presentation. A format controls display; an informat interprets input. Applying a date format does not turn a character date or datetime seconds into date days. SAS dates count days from 01JAN1960; datetimes count seconds from that epoch. Confirm input unit, parsing informat, locale, timezone meaning and intended date boundary. Reject ambiguous text or report it in an exception output; do not silently reinterpret it.
3. Choose explicit conversion with target-documented `INPUT`/`PUT` and an appropriate informat/format when a type change is intended. Inspect failed conversion and resulting missing values. Do not hide invalid-data diagnostics with suppression modifiers merely to remove log notes; if approved exception handling suppresses diagnostics, retain explicit failure counts and rejected records without sensitive values.
4. Specify missing-value policy separately for filtering, arithmetic, grouping and joining. The cited Base SAS numeric ordering puts numeric missing values below nonmissing numbers, so `x < 0` is not a nonmissing-negative test. Ordinary `.` and special numeric missings are distinct; `x = .` alone is not a check for every numeric missing value. Use a target-documented missing predicate when all missing kinds are intended. Do not replace a valid missing category with zero without domain authorization.
5. For each output column, state source, type/length, unit, derivation and missing policy. For each output row, state grain and inclusion rule. Finish with an expected schema and independently derived counts/totals.

## Choose DATA step or SQL by intended row semantics

1. For a sequential DATA step, trace compile-time attributes and per-observation state. Explicitly account for retention, sum statements, explicit/implicit OUTPUT, subsetting IF and input WHERE timing. For BY processing, verify ordered/grouped input requirements and initialize any group accumulator deliberately. `FIRST.`/`LAST.` identify boundaries of the documented BY grouping, not an arbitrary business tie-breaker.
2. For a match-MERGE, confirm common BY keys and compatible types/lengths. The cited MERGE documentation requires inputs sorted by BY keys or an appropriate index. Shared non-key column names need deliberate renaming/precedence; otherwise overwrite behavior can obscure provenance. Repeated keys on both sides do **not** form all pairs. Never replace a SQL join with MERGE solely as an optimization.
3. For PROC SQL, specify join type, complete predicate, expected key multiplicity and unmatched-row policy. In the cited PROC SQL edition, missing join keys can match; do not import ANSI NULL expectations. If missing keys must never match, express that policy in the join predicate. Keep predicates on the nullable side of an outer join in the correct clause for the requested preservation rule.
4. For summaries, select only grouping keys and intended aggregates unless row-level remerging is deliberate. The cited PROC SQL can remerge aggregate results with original rows, and GROUP BY without an aggregate can become ordering rather than reduction. Inspect the log and verify output grain. `COUNT(*)` counts rows; `COUNT(column)` counts nonmissing column values. These are not interchangeable denominators.
5. Compare distinct keys, multiplicities, unmatched groups and control totals before accepting the result. A row explosion may be correct for an all-pairs relationship; a small result may be wrong if pairs were lost. Do not use DISTINCT, deduplication or a row limit as an unrequested fix.

## Synthetic repeated-key fixture: MERGE is not an SQL join

Original illustrative code, not executed. Intended for authorized Compute/Base execution only after target binding. All names below must be reserved, task-owned WORK members: running it can overwrite them. Synthetic inputs are sorted as entered and contain no missing keys or overlapping non-key names.

```sas
data work.sp_left;
  input key left_value;
  datalines;
10 4
10 9
20 6
;
run;
data work.sp_right;
  input key right_value;
  datalines;
10 100
10 200
10 300
30 400
;
run;

data work.sp_merge;
  merge work.sp_left work.sp_right;
  by key;
run;

proc sql;
  create table work.sp_pairs as
  select l.key, l.left_value, r.right_value
  from work.sp_left as l inner join work.sp_right as r
    on l.key = r.key;
  select count(*) as merge_rows from work.sp_merge;
  select count(*) as pair_rows from work.sp_pairs;
quit;
```

**Independent oracle, derived before execution:** left has 3 rows and right has 4. At key 10, multiplicities are 2 and 3. Unfiltered match-MERGE emits `max(2,3)=3` observations for that group, then one each for keys 20 and 30: **5 total**. Expected triples `(key,left_value,right_value)` are `(10,4,100)`, `(10,9,200)`, `(10,9,300)`, `(20,6,.)`, `(30,.,400)`. The short side's last value is retained within the repeated group; it is not carried into a different key group.

The inner equijoin emits `2 × 3 = 6` pairs at key 10 and none for keys 20 or 30: **6 total**, the set `{4,9} × {100,200,300}` with key 10. SQL row order is not asserted. These different inclusion rules are deliberate; even if both outputs were restricted to shared keys, the repeated-key outputs would still differ: 3 MERGE observations versus 6 SQL pairs. A second MERGE with an explicit shared-group filter would be required to observe that alternative; it is not what the shown code runs.

Before accepting a live result, compare actual triples, key counts and missing fields, not only 5 versus 6. Inspect the MERGE log for repeated-BY-key diagnostics and ensure the intended input ordering is still true. Expected counts are reasoned expectations, not claimed SAS results. A different business goal requires choosing its proper grain, not making the two programs produce matching totals artificially.
