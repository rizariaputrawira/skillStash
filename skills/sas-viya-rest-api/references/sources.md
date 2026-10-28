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
