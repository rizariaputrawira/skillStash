# Sources and version boundaries

## Authorship and evidence status

This portable skill and its examples are independently authored from first-party SAS documentation and public repository evidence. They replace an earlier draft; they are not an upstream SWAT implementation, official SAS skill, or copied SAS tutorial. Research observation date: **2026-10-28**. No SWAT package was installed and no example, authentication flow, CAS action, or live Viya deployment was executed or validated during authoring.

The folder is self-contained: `SKILL.md` supplies the main procedure and `references/cas-workflows.md` supplies conditional operational detail. No sibling skill, root catalog, proprietary authoring framework, or special loader is required to read it. Documentation links are evidence and refresh locations, not bundled dependencies or grants of system access.

## SWAT documentation and repository

The public documentation index observed on the research date identifies **SWAT 1.18.1**, index date **June 30, 2026**. The release notes label 1.18.1 **June 29, 2026**. These are distinct published dates, not a live compatibility result. The documentation site and repository `master` links are moving sources, not immutable snapshots or promises of the newest available release.

| Official source | Exact URL | Observation used |
| --- | --- | --- |
| SWAT documentation index | https://sassoftware.github.io/python-swat/ | Identifies SWAT as a Python CAS client and supplies the observed version/date. |
| Getting Started | https://sassoftware.github.io/python-swat/getting-started.html | `swat.CAS`, HTTPS proxy-path example, `serverStatus`, action-set loading, CASResults, authinfo, OAuth via password with no username, HTTP authorization code/PKCE, and binary-only Kerberos. Documents the Viya 3.5 `CASSPN` override for SWAT 1.8.0 onward. |
| Installation | https://sassoftware.github.io/python-swat/install.html | Observed 1.18.1 Python 3.7–3.14 prerequisite ranges, binary artifacts/TK, Linux library dependencies, and pure-Python REST distinction. Not a complete tested environment matrix. |
| Binary vs. REST | https://sassoftware.github.io/python-swat/binary-vs-rest.html | Explicit `protocol="cas"`, protocol/port agreement, binary native/TK dependencies, REST conversion overhead, and absence of REST data message handlers. |
| Encryption | https://sassoftware.github.io/python-swat/encryption.html | Hostname matching, local `CAS_CLIENT_SSL_CA_LIST` PEM path, and Windows binary certificate-store distinction. Historical troubleshooting library filenames are not adopted as current requirements. |
| Loading Data | https://sassoftware.github.io/python-swat/loading-data.html | `read_csv` parses via client pandas, `upload_file` transfers bytes for server parsing, URLs traverse the client, `load_path` uses CAS-side caslib-relative sources, connector-resource distinction. |
| Table versus DataFrame | https://sassoftware.github.io/python-swat/table-vs-dataframe.html | `CASTable`/`CASColumn` references versus local `SASDataFrame`/DataFrame data, and whole-table transfer risk of `to_frame()`. Do not treat old method names appearing on this conceptual page as current APIs; for example its `ix` mention is superseded by the 1.6.0 removal recorded in the changelog. |
| CAS constructor/API | https://sassoftware.github.io/python-swat/generated/swat.cas.connection.CAS.html | Concrete constructor parameters, protocol/endpoint agreement, session attachment, upload methods, discovery helpers, and lifecycle methods. |
| Action retrieval | https://sassoftware.github.io/python-swat/generated/swat.cas.connection.CAS.retrieve.html | `retrieve` aggregates into CASResults; an invalid action example returns severity 2 rather than necessarily raising. |
| CASResults API | https://sassoftware.github.io/python-swat/generated/swat.cas.results.CASResults.html | `severity` 0/1/2 semantics; `messages`, `reason`, `status`, `status_code`, performance, and dictionary result access. |
| Disconnect | https://sassoftware.github.io/python-swat/generated/swat.cas.connection.CAS.close.html | Signature `close(close_session=False)`; a plain close is not an explicit session termination. |
| Terminate | https://sassoftware.github.io/python-swat/generated/swat.cas.connection.CAS.terminate.html | `terminate()` ends the session and closes the connection. |
| Release notes | https://sassoftware.github.io/python-swat/whatsnew.html | 1.18.1 OAuth updates; 1.18.0 Python 3.14/TK updates; 1.14.0 PKCE; historic API changes. Package features have their own version axis. |
| SAS-owned repository | https://github.com/sassoftware/python-swat | README and source identity; binary-versus-source distribution distinction. Repository observation is not a pinned release audit. |
| Upstream license statement, pinned SWAT v1.18.1 | https://github.com/sassoftware/python-swat/blob/v1.18.1/LICENSE.md | The tagged license identifies Apache-2.0 for SWAT Python source and separate SAS License Agreement terms for bundled TK libraries and C extensions. This skill redistributes neither. |

## Official CAS action documentation

The modern edition consulted is **SAS Viya Platform Programming documentation collection `pgmsascdc/v_072`**, identified in the official search result as **2026.02**. The content pages below carry **2026-02-16** metadata. This fixed collection was selected as an observed action-reference baseline, not as the newest edition or the target deployment's edition. Use the actual deployment's release selector and live action metadata for implementation decisions.

SAS Help Center page URLs can return only the JavaScript application shell to static readers. The following exact first-party content endpoints returned the actual documentation during research:

| Action/topic | Exact official URL | Evidence used |
| --- | --- | --- |
| Builtins action list | https://documentation.sas.com/api/collections/pgmsascdc/v_072/docsets/caspg/content/cas-builtins-TblOfActions.htm?locale=en | `help`, `reflect`, `loadActionSet`, `serverStatus` and their purposes. |
| Help | https://documentation.sas.com/api/collections/pgmsascdc/v_072/docsets/caspg/content/cas-builtins-help.htm?locale=en | `action` and `actionSet` parameters, qualified action names. |
| Reflection | https://documentation.sas.com/api/collections/pgmsascdc/v_072/docsets/caspg/content/cas-builtins-reflect.htm?locale=en | `action`, `actionSet`, nested parameter levels and detailed metadata. |
| Table details/results | https://documentation.sas.com/api/collections/pgmsascdc/v_072/docsets/caspg/content/p1xt9526uq5etwn1vmnk8koh0k6y.htm?locale=en | Caslib/source distinctions, `FileInfo` versus `TableInfo`, table result keys, `RecordCount.N`, existence values 0/1/2, same-named table drop hazard, and release-specific type behavior. |
| Existence | https://documentation.sas.com/api/collections/pgmsascdc/v_072/docsets/caspg/content/cas-table-tableexists.htm?locale=en | `name`, `caslib`, integer `exists`; modern `checkExternal` exists but is not assumed for older targets or used as a universal persistence check. |
| Fetch | https://documentation.sas.com/api/collections/pgmsascdc/v_072/docsets/caspg/content/cas-table-fetch.htm?locale=en | Explicit input table, `fetchVars`, `to`, `maxRows`, `index`, sorting parameters. |
| Count | https://documentation.sas.com/api/collections/pgmsascdc/v_072/docsets/caspg/content/cas-table-recordcount.htm?locale=en | `table.recordCount` with input-table metadata and optional grouping. |
| Promote | https://documentation.sas.com/api/collections/pgmsascdc/v_072/docsets/caspg/content/cas-table-promote.htm?locale=en | Source/destination names, global scope, default `drop=True`, no `replace` parameter, and `quiet` masking a missing source. |
| Promotion example | https://documentation.sas.com/api/collections/pgmsascdc/v_072/docsets/caspg/content/n16fv7nbuskc9wn1xh1y0igql4qc.htm?locale=en | Promotion changes a session table to global scope; independently rewritten examples avoid upstream replacement defaults. |
| Save | https://documentation.sas.com/api/collections/pgmsascdc/v_072/docsets/caspg/content/cas-table-save.htm?locale=en | Distinct backing-store write; input table, destination caslib/name, export options and replacement control. |

The **Viya 3.5** action baseline was independently consulted, not inferred from the modern page:

- https://documentation.sas.com/api/docsets/caspg/3.5/content/cas-table-TblOfActions.htm?locale=en — *SAS Viya 3.5: System Programming Guide*, page metadata/last update **2025-07-15**. Lists table loading, inspection, promotion, saving, drop and delete-source actions.
- https://documentation.sas.com/api/docsets/caspg/3.5/content/p1xt9526uq5etwn1vmnk8koh0k6y.htm?locale=en — same guide/date; caslib/session/global distinctions, server backing sources versus in-memory tables, saving, and table results. An editorial update in 2025 does not turn Viya 3.5 into a modern platform release.
- Human-facing 3.5 action-list locator: https://go.documentation.sas.com/doc/en/pgmsascdc/9.4_3.5/caspg/cas-table-TblOfActions.htm — static retrieval exposed the Help Center shell; the content endpoint above supplied the actual text.

## Platform naming and scope

- https://support.sas.com/en/software/sas-viya-platform.html — official documentation entry page observed on 2026-10-28. Separates modern platform documentation from previous Viya 3.5 documentation; the older SWAT-linked URL https://support.sas.com/documentation/onlinedoc/viya/index.html redirected here.
- https://support.sas.com/en/technical-support/services-policies/sas-viya-platform.html — observed policy page describes Stable monthly and LTS six-month cadences and `YYYY.MM` release naming, explicitly distinct from Viya 3.x. It is not evidence of the support status of any unknown installed deployment.

## Limitations and provenance boundaries

- No compatibility matrix is asserted for combinations of Python, pandas, SWAT artifacts, operating systems, CAS server builds, authentication configurations, connectors, or Viya updates. A documented method name does not establish availability on an older server. Live `help`/`reflect`, package release notes, selected artifacts, and deployment policy remain necessary.
- The documentation is heterogeneous: old conceptual pages contain historical APIs and old TLS troubleshooting examples alongside newer reference pages. This skill uses verified current constructor/result APIs at its stated baseline and explicitly routes legacy/server-specific behavior to matching sources.
- A small fetched sample is local data and not proof of durability or analytic correctness. Metadata/count/fetch checks are separate actions, not an atomic snapshot. No live performance or access-control outcome is claimed.
- The package contains original prose and original compact examples, not upstream Python modules, binaries, or copied documentation bodies. The upstream repository's license split is recorded as provenance, not applied to SAS documentation or used to invent a license for this skill. SAS documentation, software licensing, product entitlements, and this repository's own licensing decisions remain distinct.

## License boundary

The SWAT license observation is version-pinned to `v1.18.1` and does not assign a license to SAS Help Center documentation or to this original-prose skill. No SWAT source, wheel, binary, TK library, C extension, copied documentation body or license text is bundled. The Python examples are independently written small recipes, not verbatim upstream examples. This repository does not declare a license for its original texts. The relevant [tagged license file](https://raw.githubusercontent.com/sassoftware/python-swat/v1.18.1/LICENSE.md) includes the separate terms; do not treat SWAT's Apache grant as covering those bundled native components or SAS Viya entitlements.

## Quick-start learning source

Supplementary reference observed **2026-10-28**: [SAS Viya Quick Start](https://github.com/sassoftware/sas-viya-quick-start/tree/f332021071e4667eedab16e690ab1d8a7ac93c4e), commit **`f332021071e4667eedab16e690ab1d8a7ac93c4e`** (2026-10-20). Inspected pinned [README](https://github.com/sassoftware/sas-viya-quick-start/blob/f332021071e4667eedab16e690ab1d8a7ac93c4e/README.md), [Compute_CAS_CASL.sas](https://github.com/sassoftware/sas-viya-quick-start/blob/f332021071e4667eedab16e690ab1d8a7ac93c4e/Compute_CAS_CASL.sas), and [_Load_Quick_Start_Data.sas](https://github.com/sassoftware/sas-viya-quick-start/blob/f332021071e4667eedab16e690ab1d8a7ac93c4e/_Load_Quick_Start_Data.sas) for execution-location contrasts, path assumptions, table drops, promotion and backing-store replacement. These are teaching examples, not the authority for SWAT signatures or server support; the SWAT/action baselines above remain unchanged.

The pinned [LICENSE](https://github.com/sassoftware/sas-viya-quick-start/blob/f332021071e4667eedab16e690ab1d8a7ac93c4e/LICENSE) declares Apache-2.0 and the inspected SAS files carry matching headers. No code, data, notebooks, images or video content is copied into this package. Linked videos were not watched; notebooks, dataset provenance and other assets were not audited. No upstream example was executed. This reference observation does not establish redistribution rights over every dataset/video or assign a license to Skillstash text.
