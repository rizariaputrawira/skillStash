# Sources, versions and licensing

## Baseline and authorship

Research observed **2026-10-28**. The upstream `main` branch resolved to commit **`fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3`** (commit dated 2026-10-27). This package uses commit-pinned sources, not an asserted released binary or a server support matrix. GitHub release/tag queries returned empty lists during research; the changelog contains only a `v0.1.0` heading dated 2026-01-10. A heading does not establish an available tag/artifact, and moving branch content must not be described as v0.1.0 behavior.

The skill is independently authored guidance and original compact command/config examples. No upstream Go source, generated documentation body, binary, asset, dependency or license text is bundled. It requires no sibling skills, root catalog, Kitshift or Leanflow files to be read. Naming SAS Workbench and `wbctl` is descriptive, not SAS endorsement or a software entitlement.

## Primary source record

| Source | Observed use |
| --- | --- |
| [Resolved commit](https://github.com/sassoftware/wbctl/commit/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3) | Exact source baseline; profiles added by this commit |
| [Pinned README](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/README.md) | Workbench-only scope, config path/WBCONFIG, profiles/defaults, device/PKCE auth and command families |
| [Pinned changelog](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/CHANGELOG.md) | Sparse historical version evidence; not a release compatibility matrix |
| [Releases endpoint](https://api.github.com/repos/sassoftware/wbctl/releases?per_page=2) and [tags endpoint](https://api.github.com/repos/sassoftware/wbctl/tags?per_page=5) | Empty at research time; mutable observations, not a permanent claim |
| [Create job help](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/doc/wbctl%20create%20job.md) | Application/services, command, context, cron, dataplane, environment, volumes, JSON filename and dry-run |
| [Create submission help](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/doc/wbctl%20create%20job-submission.md) | Job ID, placement/environment overrides and dry-run flag |
| [Submission creation source](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/internal/cmd/create/job_submission/job_submission.go) | Auth/resource resolution before dry-run; live create prints ID; start jobs routes to new submission |
| [Get submission help](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/doc/wbctl%20get%20job-submissions.md) | ID selection and json/text output; no documented wait/watch flag |
| [Describe help](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/doc/wbctl%20describe%20job-submissions.md), [logs help](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/doc/wbctl%20logs%20job-submissions.md), [cancel help](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/doc/wbctl%20cancel%20job-submission.md) | Separate observation/log/cancellation operations and ID requirements |
| [Submission service source](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/internal/services/workbench/job_submissions.go) | ID-or-controller-ID query, state/diagnostic representation, request-body logging and describe output including environment |
| [Token store source](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/internal/auth/token_store.go) | Per-user token cache distinct from configuration; cached credential fields and profile-prefixed normalized endpoint keys, not organization-specific keys |
| [Root LICENSE](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/LICENSE) | Apache-2.0 grant/redistribution clauses; inspected source headers also identify Apache-2.0 |
| [Third-party license inventory](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/licenses/licenses.md) | Upstream carries dependency-specific license files; inventory observed, not full transitive verification |

The main-branch workbench-create and volume-delete help were also inspected during initial assessment. No stronger claims about server persistence, cascades or backups are derived from them; the resource reference requires target-specific verification. The pinned README's multi-profile JSON contains syntax errors; the skill uses its own syntactically valid synthetic configuration rather than copying that example.

## License and incorporation boundaries

- **Upstream source:** the pinned root license is Apache-2.0. If source is incorporated/distributed later, review section 4: license copy, modification notices, retained source notices and applicable NOTICE attribution where upstream includes it. This skill only references the interfaces; it does not redistribute that source or binary.
- **Dependencies:** upstream's `licenses/` inventory lists third-party components, including Go libraries and Kubernetes packages. Individual license files, the complete build graph and any distributed binary were not comprehensively audited. The root Apache label is not a blanket dependency clearance.
- **Workbench and workloads:** the CLI license does not establish rights to use a Workbench service, proprietary SAS software, application images, models or data processed by a job. Establish relevant entitlements/terms separately.
- **Local guidance:** original prose and examples, no copied implementation or third-party runtime included. Skillstash currently has no repository-wide license for its own text; references do not assign one. This is a source/provenance record, not legal clearance or a declaration that historical authorship is proven.

## Runtime and orchestration limits

No `wbctl` executable was available on PATH during authoring. No CLI was installed, no login/token-cache contents inspected, and no server operation, job launch, schedule, cancellation, image build, volume or snapshot action was exercised. Offline syntax/portable-package smoke checks, when reported, establish only their stated layer—not native host discovery or an actual consuming-agent run.

The inspected evidence establishes commands and selected client behavior, not native DAG scheduling, atomic deduplication, exactly-once execution, terminal-state meanings, cron timezone/overlap policy or cross-system orchestration guarantees. Those remain deployment-specific questions. A coordinator may use the CLI, but must own dependency gating, outcome reconciliation and bounded execution without inventing unsupported CLI/API fields.
