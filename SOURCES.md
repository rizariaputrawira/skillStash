# Sources and provenance

The skills are independently authored procedural guidance informed by first-party SAS sources. They do not bundle upstream implementation code, claim SAS endorsement, or inherit an upstream project's license merely by linking it. Referenced projects/documentation retain their own terms. This repository currently declares no license for its original skill texts.

## Package-local source records

Each selectable folder carries its own consulted URLs, version observations, and limitations. Its local sources reference is the canonical detailed record; this index is for repository discovery, not an installation dependency.

| Package | Source basis |
| --- | --- |
| [`sasctl-python`](skills/sasctl-python/SKILL.md) | [Local source record](skills/sasctl-python/SOURCES.md): [SAS python-sasctl](https://github.com/sassoftware/python-sasctl), documentation baseline **1.12.0**, release-tagged source, and model lifecycle references |
| [`sas-viya-python-cas`](skills/sas-viya-python-cas/SKILL.md) | [Local source record](skills/sas-viya-python-cas/SOURCES.md): [SAS python-swat](https://github.com/sassoftware/python-swat), documentation baseline **1.18.1**, and separate modern/Viya 3.5 CAS action references |
| [`sas-viya-rest-api`](skills/sas-viya-rest-api/SKILL.md) | [Local source record](skills/sas-viya-rest-api/references/sources.md): [SAS REST catalog](https://developer.sas.com/rest-apis), Logon v1, Compute v24, Job Execution v7, and official platform release documentation |

## Version evidence

Research snapshot: **2026-10-28**. Package documentation versions and selected API versions are evidence of consulted material, not a support matrix, a newest-release claim, or evidence of an installed server version. The detailed local source records distinguish documentation, repository source, and unresolved compatibility.

The official [SAS Help Center](https://documentation.sas.com/) exposes separate Viya 3.5 and SAS Viya platform documentation. Its [Release Schedule and Versions](https://documentation.sas.com/doc/en/itopscdc/v_073/itopscon/n0skwn6305faxnn1v0lfhzssr41u.htm) explains Stable versus LTS numbering and patch timing. Select target-matched product documentation, service contracts, client package release notes and actual capabilities together; these version axes cannot substitute for one another.

## Community and evidence limits

The user supplied the [SAS Developers community](https://communities.sas.com/t5/Developers/bd-p/developers). Direct retrieval returned **HTTP 403**; posts were not reviewed or used as evidence. Community material may inform future troubleshooting after its release/context is checked against official sources and the deployment.

No live SAS Viya/CAS environment was supplied or exercised. Package validation and local examples do not establish authentication, permissions, remote execution, publishing support, or runtime host discovery. Keep those claims separate when using or maintaining the skills.

## Maintaining a skill

When changing version-sensitive advice, re-open the owning official source and record the observed version/date in that skill's local source record. Prefer release-tagged source or resolved versioned docs when available. Record inaccessible or moving sources honestly. Preserve upstream license/notice requirements if future changes actually import material; do not assume this original-prose provenance statement covers copied code or documentation.
