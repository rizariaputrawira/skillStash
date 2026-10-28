# Select a deployment-compatible contract

## Record independent version axes

| Axis | Evidence to record | Do not substitute |
| --- | --- | --- |
| Deployment family | Viya 3.5 versus SAS Viya platform, from deployment records or administrator | A Python package's Viya badge |
| Platform release | Release identifier, Stable or LTS where applicable, installed update/patch evidence | Today's date or latest public docs |
| Service API | Service name, selected catalog/OpenAPI version, target support evidence | Platform release number |
| Representation | Operation-specific media type and schema `version` | API catalog suffix |
| Client | Runtime, installed package versions and lockfile when applicable | Documentation site version alone |

SAS's [Release Schedule and Versions](https://documentation.sas.com/doc/en/itopscdc/v_073/itopscon/n0skwn6305faxnn1v0lfhzssr41u.htm) describes Stable monthly releases and LTS releases about every six months. Both use `yyyy.mm`, but an LTS number identifies the Stable code base, not necessarily the month LTS shipped. Critical patch updates can occur outside that schedule. Do not derive deployment compatibility from the month alone.

The [SAS Help Center](https://documentation.sas.com/) distinguishes Viya 3.5 from SAS Viya platform documentation. Choose the applicable product/release in the documentation selector; record the resolved versioned page, not only a moving `default` link. Internal documentation collection identifiers such as `v_073` are not a substitute for the installed platform release.

## Selection procedure

1. Obtain the target's release/cadence evidence without collecting credentials or exporting an entire deployment configuration. If unavailable, ask only for the missing non-secret identifier after inspecting available authorized records.
2. Find the relevant API in the [catalog](https://developer.sas.com/rest-apis). At research time it listed Compute v24 and v23 and several older contracts; these are alternatives, not a target support matrix.
3. Open the matching versioned index and OpenAPI; compare the requested operation, required fields, content types, response status, paging and asynchronous semantics. Use deployment-specific docs or advertised capabilities to establish target support. A root response can advertise links without identifying every version axis.
4. When migrating, compare old and new contracts for the operations actually used. Also check release notes and authorization changes. Do not rewrite every request to the highest public API version or assume a `/v24` runtime path.
5. Label evidence as **observed on target**, **documented for target**, or **unverified**. If target support remains unknown, prepare conditional implementation and a safe verification step; do not run a speculative mutation.

## Compatibility report

Include: deployment family; release/cadence/patch evidence; selected service/API contract URL; request representation; client/runtime versions when relevant; exact operation exercised; observed result; unresolved support facts. `Unknown` is preferable to an inferred compatibility matrix.

Sources consulted 2026-10-28. This is a selection method, not a maintained list of supported releases.
