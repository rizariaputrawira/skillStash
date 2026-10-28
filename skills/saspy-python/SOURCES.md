# Sources, versions and license boundaries

## Identity and research baseline

Independently authored procedural guidance and compact examples for using SASPy. No upstream Python, SAS program, Java source/JAR, notebook, image, documentation body or license text is bundled. The skill has no dependency on sibling skills, repository-root documents, Kitshift or Leanflow. References establish source authority, not SAS endorsement, server access or software entitlements.

Research date: **2026-10-28**. Baseline release **v5.110.0**, published **2026-10-20**; tag resolved to commit **`ab3c55f0b6366418f5c63ab5c0d81e1c1bca51d3`**. This is a research pin, not a bundled dependency or an instruction to upgrade. Release tags can move; the commit is the reproducible identity. Hosted docs and `main` can change independently. No universal Python/SAS/Java/pandas compatibility matrix is asserted; inspect the installed distribution and target requirements before execution.

## Primary evidence

All repository file links below use the observed release tag; substitute the resolved commit for immutable comparisons.

| Source | Evidence used |
| --- | --- |
| [Release v5.110.0](https://github.com/sassoftware/saspy/releases/tag/v5.110.0) | Release identity/date; new PROC COPY wrapper |
| [Tag resolution](https://api.github.com/repos/sassoftware/saspy/git/ref/tags/v5.110.0) | Commit identity observed during research |
| [Tagged README](https://github.com/sassoftware/saspy/blob/v5.110.0/README.md) | Python/SAS integration scope; broad prerequisites are not a tested support matrix |
| [Tagged configuration](https://github.com/sassoftware/saspy/blob/v5.110.0/saspy/doc/source/configuration.rst) | HTTP/Viya Compute versus SAS 9.4/IOM, SSH/STDIO/COM, config discovery, lock_down, token/authcode/context and CA configuration |
| [Tagged session source](https://github.com/sassoftware/saspy/blob/v5.110.0/saspy/sasbase.py) | `SASsession`, `submit` LOG/LST, colored logs, `lastlog`, `endsas`, data-transfer signatures/index handling and timestamp options; only relevant sections inspected |
| [Tagged HTTP source](https://github.com/sassoftware/saspy/blob/v5.110.0/saspy/sasiohttp.py) | Effective verify/cafile settings, PKCE authorization-code construction and `verify=None` unverified fallback; only relevant sections inspected |
| [Tagged SASdata source](https://github.com/sassoftware/saspy/blob/v5.110.0/saspy/sasdata.py) | Dataset options, `.to_df()` forwarding and invalid-dataset result behavior; only relevant sections inspected |
| [Tagged limitations](https://github.com/sassoftware/saspy/blob/v5.110.0/saspy/doc/source/limitations.rst) | Log synchronization, PRINTTO reset and ABORT CANCEL differences between transports |
| [Tagged changelog](https://github.com/sassoftware/saspy/blob/v5.110.0/CHANGELOG.md) | 5.110.0 copy; 5.109.x conversion/schema and local IOM process fixes |
| [Tagged license](https://github.com/sassoftware/saspy/blob/v5.110.0/LICENSE.md) | Component-specific Apache/SAS license split |
| [Tagged third-party notice](https://github.com/sassoftware/saspy/blob/v5.110.0/saspy/java/thirdparty/NOTICE.md) | Eclipse ORB declared license alternatives and third-party notices; not a complete JAR/dependency audit |
| [Hosted documentation](https://sassoftware.github.io/saspy/) | Moving documentation entry point; use matching source for version-sensitive behavior |

## Version changes relevant to consumers

- **5.110.0 / 2026-10-20:** new PROC COPY wrapper; its presence cannot be assumed on older clients.
- **5.109.4 / 2026-10-16:** extended-attribute case handling in schema resolution fixed.
- **5.109.3 / 2026-09-18:** non-Arrow Parquet path date/time/datetime conversion fixed.
- **5.109.2 / 2026-09-17:** HTTP date/time/datetime detection fixed.
- **5.109.1 / 2026-09-17:** Arrow/Parquet type handling fixes, including silent null conversions, changed date/time types and `include_attrs` default.
- **5.109.0 / 2026-09-11:** empty Arrow/Parquet exports, HTTP DATE handling and local Windows IOM extra-process cleanup addressed.

These facts justify target-specific conversion tests, not an unconditional newest-version requirement. A successful local package import does not verify a server session, licensed procedures, context identity, TLS or business-result correctness.

## Licensing findings

| Material | Observed terms at v5.110.0 | Local incorporation / boundary |
| --- | --- | --- |
| SASPy Python code | Root LICENSE identifies Apache-2.0 for Python code | Referenced APIs only; no source copied. Apache redistribution conditions become relevant if source is later incorporated, not merely cited. |
| IOM Java Client | Root LICENSE separately applies the SAS License Agreement for Corrective Code or Additional Functionality to the IOM Java Client JARs (its wording says four JARs) | No JARs bundled. Verify exact installed artifacts and SAS entitlement; do not characterize the entire wheel as Apache-only or assume the license's count is an inspected binary inventory. |
| Third-party Java content | Tagged NOTICE declares Eclipse ORB alternatives: EPL-2.0 OR BSD-3-Clause OR GPL-2.0 WITH Classpath-exception-2.0, plus other third-party notices | No binaries bundled; no alternative selected on the user's behalf. Exact JAR versions, transitive contents and distribution compliance were not audited. |
| SAS software and official documentation | Separate SAS product/documentation terms; Python package license does not supply SAS software rights | No SAS runtime or copied documentation bodies bundled. |
| Skillstash guidance/examples | No repository-wide license currently declared | Original text does not inherit upstream licenses. This record does not assign a license or provide legal clearance. |

Before future vendoring or distribution of source/JARs/images, inspect exact artifacts, file-level terms, required license copies, modification notices, source notices and applicable NOTICE attributions. This source review does not establish all dependency obligations or historical copying provenance elsewhere in the repository.

## Verification limits

No SASPy package was installed/imported, no SAS session started, no OAuth credential acquired, and no SAS program, transfer, IOM/SSH transport, cancellation or live cleanup exercised during authoring. The examples are source-checked recipes, not observed SAS outputs. Offline package/link/syntax checks, when reported, do not establish native host discovery or consuming-agent behavior. Runtime verification requires an authorized target and non-sensitive expected results.
