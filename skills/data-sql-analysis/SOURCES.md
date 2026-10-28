# Sources and provenance

## Authorship and scope

`data-sql-analysis` is independently authored analytical guidance with a compact original synthetic SQL fixture. No upstream prose, example query, code or assets were copied or adapted. Official documentation was consulted to check the bounded technical semantics below, not imported as skill instructions. The package is passive guidance; source access and skill selection confer no operation authority.

All consultations below occurred on **2026-10-28**. The consulted URLs select the **PostgreSQL 17 manual**; the rendered manual identified itself as PostgreSQL **17.11 Documentation**. These are major-version documentation URLs, not immutable content hashes or deployment compatibility guarantees. Do not follow a page's `/current/` canonical metadata and silently change the recorded edition. The actual target engine, release, configuration, privileges, schema and execution surface must still be bound for each assignment. No SQL engine execution was performed during authoring.

## Official sources actually consulted

| Exact official URL | Consulted edition | Technical claim supported / limit |
| --- | --- | --- |
| https://www.postgresql.org/docs/17/sql-select.html | PostgreSQL 17, SELECT command | `WITH`, `VALUES` input forms, joins, grouping, ordering, casts in query expressions; modifying CTEs and locking clauses; no guarantee a CTE materializes or improves speed. Not SAS or other-engine syntax evidence. |
| https://www.postgresql.org/docs/17/queries-table-expressions.html | PostgreSQL 17, §7.2 Table Expressions | Join multiplicity, outer joins, explicit predicates and `ON` versus `WHERE` filtering. Business grain/cardinality must be established from the actual data contract. |
| https://www.postgresql.org/docs/17/functions-aggregate.html | PostgreSQL 17, §9.21 Aggregate Functions | `COUNT(*)` versus non-null counts, aggregate null/empty-input behavior and sum/average semantics. Does not establish a domain's correct metric or production performance. |
| https://www.postgresql.org/docs/17/sql-explain.html | PostgreSQL 17, EXPLAIN command | Estimated cost versus measured execution, `ANALYZE` executes the statement, profiling overhead and plan variability. Does not authorize execution, establish a runtime plan or prove cost/billing/speed. |
| https://www.postgresql.org/docs/17/functions-window.html | PostgreSQL 17, §9.22 Window Functions | Partition ordering, peers, `LAG`, ranking and frame-sensitive aggregates/value functions. No claim of window support in PROC SQL, FedSQL or another engine. |
| https://www.postgresql.org/docs/17/functions-conditional.html | PostgreSQL 17, §9.18 Conditional Expressions | `COALESCE`, `NULLIF`, common result types and conditional evaluation caveats; basis for explicit zero-denominator/null handling. No universal metric missingness policy follows from syntax. |
| https://www.postgresql.org/docs/17/functions-comparison.html | PostgreSQL 17, §9.2 Comparison Functions and Operators | Ordinary comparisons involving null, `IS NULL` and null-safe comparison semantics. Not evidence for SAS missing-value semantics or automatic key normalization. |

## Evidence and rights boundaries

- General workflow choices—result contracts, independent totals, authority/privacy/budget checks and uncertainty reporting—are original engineering guidance, not quoted vendor requirements. The expected fixture values are derived by arithmetic shown beside the query, not observed database output.
- Documentation pages identify The PostgreSQL Global Development Group as their copyright holder. Their content remains subject to upstream rights and terms; links and factual consultation do not relicense it. No new license grant is made for this original package, and no repository-wide license is inferred from PostgreSQL software/documentation terms.
- No SAS technical documentation was consulted for this package and no SAS feature/version support is asserted. The SAS branch requires matched official product/release/execution-context evidence before runnable syntax or compatibility claims. Other database products likewise remain unverified, not automatically unsupported.
- Authoring/source review is distinct from package structure/link verification, actual consuming-agent behavior, host discovery and live SQL execution. Those layers require separate observed evidence; no runtime verification is claimed here.
