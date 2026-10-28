# Sources, version boundaries and licensing

## Identity and baseline

This is independently authored, MCP-aware procedural guidance for the SAS Intelligent Decisioning operations a configured SAS MCP Server exposes. It can support verified parts of a Fraud Decisioning assignment, but does not claim whole-product coverage or interchangeability. No upstream Python, deployment manifest, SDK, prompt template, UI/connector asset, documentation body or license text is bundled. Original examples are illustrative, not real policy or evidence of remote execution. The package works without siblings, root catalog, Kitshift or Leanflow.

Research date: **2026-10-28**. Inspected release **v1.19.0**, published **2026-10-23**. Its annotated tag object `30b3b0256b7c5e0da9407b0643b4174b5cd07263` resolves to commit **`e500fd4d6dac2bd46b8ae67f52934bbe6f3e381c`**. Source links below use the release tag for readability; use the resolved commit for immutable comparisons. The package's Python floor is **3.12**; release notes identify Helm chart **0.6.0** with appVersion **1.19.0**. None of these identifies the target Viya/Fraud Decisioning release, client host version or supported API capability.

## Primary evidence inspected

| Source | Observed use |
| --- | --- |
| [Release v1.19.0](https://github.com/sassoftware/sas-mcp-server/releases/tag/v1.19.0) | Release/date, expanded decisioning tools, folder requirement, MAS publication and chart version |
| [Annotated tag](https://api.github.com/repos/sassoftware/sas-mcp-server/git/tags/30b3b0256b7c5e0da9407b0643b4174b5cd07263) | Peeled commit identity |
| [Pinned README](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/README.md) | Tier scope, transport overview, strict read-only mode, runtime versus Workbench distinction |
| [Pinned changelog](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/CHANGELOG.md) | v1.19.0 revision/mapping/folder/lookup/publication changes; prior behavior cannot be assumed current |
| [Package metadata](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/pyproject.toml) | Python minimum, package version and dependency declarations; no complete dependency audit |
| [Tool registration](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/src/sas_mcp_server/tools/__init__.py) and [access classification](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/src/sas_mcp_server/tools/_access.py) | Tier/filter distinction, Tier 10 dependency warning, tool hints and compilation/scoring classification |
| [Decisioning tools](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/src/sas_mcp_server/tools/decisioning.py) | Rules/revisions, flow wiring, `steps_report`, locked versus working IDs, MAS code target, async publication/result states; relevant sections inspected |
| [Shared lifecycle tools](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/src/sas_mcp_server/tools/decisioning_lifecycle.py) | Typed asset IDs, capped/summarised reads and revision/lifecycle effects |
| [Lookup tools](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/src/sas_mcp_server/tools/decisioning_lookups.py) | Explicit versions, edits versus activation, active/running/failed outcomes and republish/rollback behavior; relevant sections inspected |
| [MAS tools](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/src/sas_mcp_server/tools/model_scoring.py) | Signature-before-score contract, execution input format and underscore fallback reporting |
| [Authentication configuration guide](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/examples/configuration.md) | PKCE/raw bearer/stdio distinctions, login-helper applicability and administrator setup; insecure example flags/CSP weakening are not adopted |
| [Server configuration](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/src/sas_mcp_server/config.py) | Auth/TLS defaults and opt-in logging/telemetry privacy boundaries; relevant section inspected |
| [Root license](https://github.com/sassoftware/sas-mcp-server/blob/v1.19.0/LICENSE) | Apache-2.0 redistribution clauses; inspected Python headers also identify Apache-2.0 |

## Version facts affecting workflow

- **1.19.0:** two decisioning tiers (7 foundation, 10 advanced), expanded content, folder-aware creates, richer steps, automatic mappings/variable additions, explicit frozen revision in lock responses and MAS-target publication. A limited deployment may need different capabilities; the skill does not change its configuration.
- **Lookup activation:** release notes say MAS/CAS reference data is published and existing published decisions use newly active entries without decision republishing. Treat this as broader runtime impact, not harmless test-data editing.
- **Publication:** v1.19.0 changes the code target to MAS; `PathID` may be empty compared with the prior path. Upstream reports its own live comparison; that is not evidence that this repository or the user's environment was tested.
- **MCP transport/tool schemas:** renaming and host-side exposure/caching can differ from repository names; live discovery remains mandatory. The built-in login helper's documented Viya 2022.11+ applicability is not an all-tools compatibility statement. Upstream says the new decisioning work was probed on Viya 2026.09; that is not a general minimum or certified matrix.

## Licensing and incorporation

| Material | Role / version | Evidence and boundary |
| --- | --- | --- |
| SAS MCP Server | Reference implementation, v1.19.0 / resolved commit above | Root Apache-2.0 license observed; no implementation or license text redistributed here. If material is copied later, inspect applicable file-level terms, retain required license/source/modification notices, and include applicable NOTICE attribution when present. |
| Server dependencies, connector/UI assets and images | Not bundled; separately installed/hosted upstream artifacts | Exact dependency/asset/image licenses and transitive contents were not audited. A root software license is not universal clearance for them or for Viya entitlements. |
| SAS software and data | Actual deployment/runtime and task inputs | Separate software/documentation/data rights and product access apply. A skill or MCP connection supplies neither license nor authority. |
| Skillstash text/example | Original local procedural synthesis and synthetic test-spec illustration | No repository-wide Skillstash license currently declared. Upstream Apache terms do not automatically license this text; this source record does not assign one or certify historical provenance. |

## Verification limits

No MCP server was installed, configured or started; no credentials/cache values inspected; no live SAS MCP discovery, Viya asset read/write, expression compilation, lookup activation, revision lock, publication or scoring performed during authoring. Offline package/example checks, when reported, prove only those layers. Native host discovery, consuming-agent behavior, exact Fraud Decisioning compatibility, authorization and business results remain unverified until exercised on an approved environment. This is source-level guidance, not legal clearance or a full security audit.
