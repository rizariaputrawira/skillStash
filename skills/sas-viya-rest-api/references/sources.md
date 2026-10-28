# Sources and provenance

Original procedural guidance informed by first-party SAS documentation; no upstream implementation code or sample payloads are bundled. Referenced materials retain their owners' terms; these links do not grant redistribution rights or imply SAS endorsement.

Consulted **2026-10-28**:

| Source | Observed scope and use |
| --- | --- |
| [SAS API catalog](https://developer.sas.com/rest-apis) | Multiple independently versioned services/contracts; discovery, not an installed-release support matrix |
| [SAS Help Center](https://documentation.sas.com/) | Separate Viya 3.5 and SAS Viya platform documentation entry points |
| [Release Schedule and Versions](https://documentation.sas.com/doc/en/itopscdc/v_073/itopscon/n0skwn6305faxnn1v0lfhzssr41u.htm) | Page last updated 2026-06-17; Stable/LTS numbering and critical patch timing, not evidence of a target's version |
| [Logon v1 index](https://developer.sas.com/rest-apis/SASLogon-v1/index.md) and [authentication notes](https://developer.sas.com/rest-apis/SASLogon-v1/docs/getting-started/authentication.md) | OAuth flow selection and scope/authority distinctions; examples include insecure TLS flags that this skill intentionally excludes |
| [Compute v24 index](https://developer.sas.com/rest-apis/compute-v24/index.md) and [OpenAPI](https://developer.sas.com/api/apis/compute-v24/specifications/openapi.yml) | API version 24; service prefix and operation contracts. OpenAPI header/root section inspected, not every operation |
| [Compute usage notes](https://developer.sas.com/rest-apis/compute-v24/docs/getting-started/usage-notes.md) | Collection/filter and request conventions; support remains operation-specific |
| [Compute createJob](https://developer.sas.com/rest-apis/compute-v24/createJob/index.md) and [getJobState](https://developer.sas.com/rest-apis/compute-v24/getJobState/index.md) | Async 201 submission, representation versus API versions, text state response, conditional polling |
| [Job Execution v7 index](https://developer.sas.com/rest-apis/jobExecution-v7/index.md) | Separate provider/job-request lifecycle and listed heartbeat error; provider contracts not exhaustively reviewed |
| [SAS MCP Server v1.19.0 README](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/README.md) and [access classification](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/src/sas_mcp_server/tools/_access.py) | Optional interface selection: configured MCP tools versus direct REST, tier/read-only filtering and effect boundaries; not proof of a connected server or complete Fraud Decisioning coverage |

The user-supplied [SAS Developers community](https://communities.sas.com/t5/Developers/bd-p/developers) returned HTTP 403 during retrieval. Its posts were not reviewed and do not substantiate this skill. Community solutions can supply troubleshooting leads, but need release/context checks against official contracts and the target.

Refresh the relevant sources when changing version-sensitive guidance. Record exact observed versions/URLs and access failures; do not call this snapshot the latest, infer an unsupported compatibility matrix, or claim live deployment validation.

## Studio step access control source

Consulted **2026-10-28**, [SAS Data and AI Studio Step Access Control](https://github.com/sassoftware/sas-data-and-ai-studio-step-access-control/tree/111f85281e48f72c1a4ae62c5e6f8b8451a0cf10), commit **`111f85281e48f72c1a4ae62c5e6f8b8451a0cf10`**. The [local reference](studio-step-access-control.md) is independently authored analysis; no upstream scripts, CSV or assets are bundled.

| Pinned source | Inspected evidence |
| --- | --- |
| [README](https://github.com/sassoftware/sas-data-and-ai-studio-step-access-control/blob/111f85281e48f72c1a4ae62c5e6f8b8451a0cf10/README.md) | Administrative prerequisites, non-public API warning, Loop-step exclusion for Stable 2026.08, intended entitlement-alignment use rather than formal compliance |
| [Create groups](https://github.com/sassoftware/sas-data-and-ai-studio-step-access-control/blob/111f85281e48f72c1a4ae62c5e6f8b8451a0cf10/Create-SAS-Groups-For-Steps.sas) | Group IDs `SASSDAIStudio` and `SASDAIStudioAdavanced`; group-creation operation labeled undocumented |
| [Create rules](https://github.com/sassoftware/sas-data-and-ai-studio-step-access-control/blob/111f85281e48f72c1a4ae62c5e6f8b8451a0cf10/Create-SAS-Authorization-Rules-For-Steps.sas) | Initial inventory, joins/filter, POST macro and principal mappings inspected; display-name mapping, `limit=10000`, no paging/reconciliation in inspected procedure, different principal names |
| [Delete rules](https://github.com/sassoftware/sas-data-and-ai-studio-step-access-control/blob/111f85281e48f72c1a4ae62c5e6f8b8451a0cf10/Delete-SAS-Authorization-Rules-For-Steps.sas) | Principal-only selection, `limit=1000`, deletion by returned ID, old tier group IDs |
| [CSV](https://github.com/sassoftware/sas-data-and-ai-studio-step-access-control/blob/111f85281e48f72c1a4ae62c5e6f8b8451a0cf10/Steps_Access_Control.csv) and [changelog](https://github.com/sassoftware/sas-data-and-ai-studio-step-access-control/blob/111f85281e48f72c1a4ae62c5e6f8b8451a0cf10/CHANGELOG.md) | CSV header/initial rows inspected; 2026.06 rename without migration, 2026.08 Loop limitation and later step additions/removal. This is not a complete entitlement map or platform support matrix. |
| [LICENSE](https://github.com/sassoftware/sas-data-and-ai-studio-step-access-control/blob/111f85281e48f72c1a4ae62c5e6f8b8451a0cf10/LICENSE) and inspected script headers | Apache-2.0. If scripts are later redistributed/adapted, retain applicable notices/license and mark modifications; review any additional incorporated material separately. These terms do not license this original reference or grant SAS product rights. |

No deployment, administrative script, group/rule mutation, effective-permission test, entitlement audit or complete upstream security review was performed. These observed source hazards warrant review before reuse, not a claim that every installation is affected.

## Content promotion sources

Consulted **2026-10-28**: pyviyatools commit **`9b7e556af33a6d9626a1c76b0813a95cd23705b3`**; [README](https://github.com/sassoftware/pyviyatools/blob/9b7e556af33a6d9626a1c76b0813a95cd23705b3/README.md), [exportfolder.py](https://github.com/sassoftware/pyviyatools/blob/9b7e556af33a6d9626a1c76b0813a95cd23705b3/exportfolder.py), [importpackages.py](https://github.com/sassoftware/pyviyatools/blob/9b7e556af33a6d9626a1c76b0813a95cd23705b3/importpackages.py), [gettransfermapping.py](https://github.com/sassoftware/pyviyatools/blob/9b7e556af33a6d9626a1c76b0813a95cd23705b3/gettransfermapping.py), [comparecontent.py](https://github.com/sassoftware/pyviyatools/blob/9b7e556af33a6d9626a1c76b0813a95cd23705b3/comparecontent.py). Inspected inventory/mapping/import/comparison mechanics inform the original [promotion reference](content-promotion.md), not a new general Python interface or executable wrapper.

[Root license](https://github.com/sassoftware/pyviyatools/blob/9b7e556af33a6d9626a1c76b0813a95cd23705b3/LICENSE) and inspected script headers identify Apache-2.0. No scripts, configurations or token handling incorporated; upstream software terms do not license original local prose or SAS services.

[Official Content Migration from SAS Viya 4 PDF](https://documentation.sas.com/api/collections/sasadmincdc/v_014/docsets/calpromotion/content/calpromotion.pdf?locale=en), edition scoped **2020.1.4–2026.01**: cover/overview, initial tasks and access-control section inspected for supported content, mappings, source/target access, transfer CLI, same-or-later releases and `options.promoteAuthorization=false`. Read target-matched documentation before execution. Official documentation is linked, not copied/relicensed. The [public transfer API lookup](https://developer.sas.com/rest-apis/transfer) returned **API Not Found**; utility code does not establish a public REST contract. A GitHub ref API lookup returned 403 during research; read-only `git ls-remote` resolved pins without cloning or changing the working tree.

No export/import, CLI setup, authentication, migration or target permission validation was performed. Original guidance and immutable source links only; candidate pins are consulted identities, not installed versions or changes since a prior incorporation.

## SCR testbench sources

Consulted **2026-10-28**: scr-testbench commit **`c0b7356a1b77af07d3862d2b5f2928d2346bb481`**; [README](https://github.com/sassoftware/scr-testbench/blob/c0b7356a1b77af07d3862d2b5f2928d2346bb481/README.md), [deploy.py](https://github.com/sassoftware/scr-testbench/blob/c0b7356a1b77af07d3862d2b5f2928d2346bb481/deploy.py), [standalone Compose](https://github.com/sassoftware/scr-testbench/blob/c0b7356a1b77af07d3862d2b5f2928d2346bb481/scr-solo/docker-compose.yml), [Dockerfile](https://github.com/sassoftware/scr-testbench/blob/c0b7356a1b77af07d3862d2b5f2928d2346bb481/scr-solo/scr/Dockerfile) and [root license](https://github.com/sassoftware/scr-testbench/blob/c0b7356a1b77af07d3862d2b5f2928d2346bb481/LICENSE). Inspected scope informs the original [conditional SCR reference](scr-runtime-testing.md): generation/revision uncertainty, TLS/token handling, logging/header/port exposure and the 2024 runtime build, not an image recommendation.

Root source license is Apache-2.0; SAS runtime images, commercial entitlements and Oracle/Redis containers have separate rights and verification requirements. No deployment script, Dockerfile, Compose file, image, module or bundled sample data incorporated. No registry authentication, pull, build, container start, SAS connection or runtime test performed. SCR applicability to the user's implementation remains unknown; this branch does not add a required deployment path.
