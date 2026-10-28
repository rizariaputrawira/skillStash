# Sources and provenance

This repository declares no repository-wide license for its independently authored skill texts. Package-local source records distinguish original guidance from upstream software terms; a link, citation, or source-version observation does not transfer upstream rights or indicate SAS endorsement. The skills contain no bundled upstream implementation code or third-party assets based on the reviewed package contents. Future copied material requires a separate file-level license and notice review.

## Package-local source records

Each selectable folder carries its own consulted URLs, version observations, and limitations. Its local sources reference is the canonical detailed record; this index is for repository discovery, not an installation dependency.

| Package | Source basis |
| --- | --- |
| [`sasctl-python`](skills/sasctl-python/SKILL.md) | [Local source record](skills/sasctl-python/SOURCES.md): [SAS python-sasctl](https://github.com/sassoftware/python-sasctl), documentation baseline **1.12.0**, release-tagged source, and model lifecycle references |
| [`sas-viya-python-cas`](skills/sas-viya-python-cas/SKILL.md) | [Local source record](skills/sas-viya-python-cas/SOURCES.md): [SAS python-swat](https://github.com/sassoftware/python-swat), documentation baseline **1.18.1**, and separate modern/Viya 3.5 CAS action references |
| [`sas-viya-rest-api`](skills/sas-viya-rest-api/SKILL.md) | [Local source record](skills/sas-viya-rest-api/references/sources.md): [SAS REST catalog](https://developer.sas.com/rest-apis), Logon v1, Compute v24, Job Execution v7, and official platform release documentation |
| [`sas-viya-agentic-ai`](skills/sas-viya-agentic-ai/SKILL.md) | [Local source/license record](skills/sas-viya-agentic-ai/references/sources.md): [SAS Agentic AI Accelerator](https://github.com/sassoftware/sas-agentic-ai-accelerator), release baseline **2.4.1**, Apache-2.0 except upstream `/static`; no accelerator code/assets bundled |
| [`saspy-python`](skills/saspy-python/SKILL.md) | [Local source/license record](skills/saspy-python/SOURCES.md): [SASPy](https://github.com/sassoftware/saspy), release **v5.110.0**, release-tagged session/configuration/conversion contracts and separate IOM Java licensing |
| [`sas-workbench-cli`](skills/sas-workbench-cli/SKILL.md) | [Local source/license record](skills/sas-workbench-cli/SOURCES.md): [wbctl](https://github.com/sassoftware/wbctl), commit **fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3**, job/submission orchestration and resource boundaries; no published version inferred from the changelog |

## License review snapshot

Reviewed **2026-10-28**, at the cited upstream release or commit baselines. This is a source-level check of the skill folders and upstream license statements, not legal advice, repository-wide licensing, an SBOM, or clearance to copy upstream material.

| Skill | Observed upstream terms | Local package finding |
| --- | --- | --- |
| `sasctl-python` | `python-sasctl` **v1.12.0** contains a tagged root Apache-2.0 `LICENSE`; the SAS copyright/NOTICE obligations apply if that software is redistributed. | Original prose and independently authored examples; no package source, notebooks, binaries, license text, or THIRD-PARTY assets copied. Upstream terms do not license this prose. |
| `sas-viya-python-cas` | `python-swat` **v1.18.1** tagged `LICENSE.md` distinguishes Apache-2.0 SWAT Python code from bundled TK libraries and C extensions under separate SAS License Agreement terms. | Original prose and examples; no SWAT source/wheels, TK/C binaries, or license file bundled. Apache-2.0 does not cover those separately licensed components or SAS entitlements. |
| `sas-viya-rest-api` | No single package upstream; SAS API documentation and each service have their own terms. The official sources are references, not software dependencies. | Original contract-focused prose and small request examples; no API implementation or verbatim sample payload set bundled. Skill text remains without a declared license here. |
| `sas-viya-agentic-ai` | Accelerator **2.4.1** README says Apache-2.0 except `/static` SAS-owned material not under an open-source license; model and dependency licenses remain separate; Viya commercial terms apply. | Original prose; no accelerator source, model, static logo, generated content package, third-party library or upstream license text bundled. |
| `saspy-python` | SASPy **v5.110.0** root license separates Apache-2.0 Python code from IOM Java Client JARs under SAS terms; bundled third-party Java content has its own notice/license alternatives. | Independently authored guidance and compact examples; no upstream source, JARs, notebooks, assets or documentation bodies bundled. See the package-local record for exact evidence and limits. |
| `sas-workbench-cli` | `wbctl` at **fd05d61** has an Apache-2.0 root license and a separate third-party license inventory; Workbench service/workload terms remain separate. | Independently authored guidance and compact shell/JSON examples; no CLI source/binary, dependencies or generated documentation bodies bundled. Full dependency/binary license audit not performed. |

## Version evidence

Research snapshot: **2026-10-28**. Package documentation versions and selected API versions are evidence of consulted material, not a support matrix, a newest-release claim, or evidence of an installed server version. The detailed local source records distinguish documentation, repository source, and unresolved compatibility.

The official [SAS Help Center](https://documentation.sas.com/) exposes separate Viya 3.5 and SAS Viya platform documentation. Its [Release Schedule and Versions](https://documentation.sas.com/doc/en/itopscdc/v_073/itopscon/n0skwn6305faxnn1v0lfhzssr41u.htm) explains Stable versus LTS numbering and patch timing. Select target-matched product documentation, service contracts, client package release notes and actual capabilities together; these version axes cannot substitute for one another.

## Community and evidence limits

The user supplied the [SAS Developers community](https://communities.sas.com/t5/Developers/bd-p/developers). Direct retrieval returned **HTTP 403**; posts were not reviewed or used as evidence. Community material may inform future troubleshooting after its release/context is checked against official sources and the deployment.

No live SAS Viya/CAS environment was supplied or exercised. Package validation and local examples do not establish authentication, permissions, remote execution, publishing support, or runtime host discovery. Keep those claims separate when using or maintaining the skills.

## Maintaining a skill

When changing version-sensitive advice, re-open the owning official source and record the observed version/date in that skill's local source record. Prefer release-tagged source or resolved versioned docs when available. Record inaccessible or moving sources honestly. Preserve upstream license/notice requirements if future changes actually import material; do not assume this original-prose provenance statement covers copied code or documentation.
