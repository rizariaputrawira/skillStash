# Skillstash

Optional, standalone AI-agent skills to select individually—not a workflow suite or Git cherry-pick mechanism. Independent of Kitshift and Leanflow.

## Principles

Inspired by [Matt Pocock's skills collection](https://github.com/mattpocock/skills): individually selectable guidance, not a framework that owns the user's workflow.

- **One distinct task per skill.** Create a standalone skill for a genuinely different trigger; extend an existing reference when the task already has an owner.
- **Independent selection.** Keep each folder self-contained, with no Kitshift, Leanflow or sibling-skill requirement.
- **Small entrypoints, conditional detail.** Put the core procedure in `SKILL.md`; load specialized references and templates only when relevant.
- **Actionable guidance.** Turn supported source evidence into usable procedures; an upstream repository does not automatically warrant a new skill.
- **User-controlled effects.** Selecting a skill does not authorize installation, authentication, deployment or administrative changes.
- **Version-aware and license-aware.** Distinguish consulted source revisions from target compatibility; preserve attribution and file-specific license boundaries.
- **No framework by imitation.** Add setup, plugins or distribution machinery only for an actual user need, not to mirror another collection.

## SAS and SAS Viya skills

| Skill | Select it when you need |
| --- | --- |
| [`sasctl-python`](skills/sasctl-python/SKILL.md) | Python integration through `sasctl`: service calls, model registration, packaging, publishing, and outcome verification |
| [`sas-viya-python-cas`](skills/sas-viya-python-cas/SKILL.md) | Python SWAT workflows: CAS connections/actions, server-side data processing, and table/session lifecycle |
| [`sas-viya-rest-api`](skills/sas-viya-rest-api/SKILL.md) | Language-independent REST integrations, OAuth, API contracts, and asynchronous SAS Compute/Job Execution |
| [`sas-viya-agentic-ai`](skills/sas-viya-agentic-ai/SKILL.md) | SAS Agentic AI Accelerator: versioned Model Definition Builder, prompt/content workflows, credentials, RAG, deployment and upgrade boundaries |
| [`saspy-python`](skills/saspy-python/SKILL.md) | Run SAS programs from Python through Viya Compute or SAS 9.4 sessions; validate logs, dataset transfers and session cleanup |
| [`sas-workbench-cli`](skills/sas-workbench-cli/SKILL.md) | Orchestrate SAS Workbench job definitions, schedules and executions with `wbctl`; verify outcomes and manage environments/storage only when authorized |
| [`sas-viya-decisioning-mcp`](skills/sas-viya-decisioning-mcp/SKILL.md) | Use discovered SAS MCP tools for supported decisioning assets, rules, lookup versions, MAS publication and business-result verification; check exact Fraud Decisioning applicability |
| [`sas-trustworthy-ai-lifecycle`](skills/sas-trustworthy-ai-lifecycle/SKILL.md) | Prepare model-governance evidence, production-readiness reviews and monitoring plans, with fraud-specific review guidance and separate owner approval |

Choose by execution surface: `sasctl` integrates with Viya services; SWAT runs CAS actions; SASPy runs SAS code through a SAS session, including Viya Compute or SAS 9.4 Workspace Server; `wbctl` controls a separate SAS Workbench installation and its jobs/resources; the accelerator adds its own Model Definition Builder and content workflows. These are not interchangeable clients or runtimes. REST guidance also applies outside Python.

For agent-operated decisioning work, inspect the tools of an already configured SAS MCP server before rebuilding REST orchestration. The skill supplies procedure; the MCP server supplies executable tools. Tool exposure, server version, Viya permissions and the exact SAS product still determine what is available. No MCP server is installed or connected by selecting a skill.

For Studio step-access administration, the REST skill contains a conditional reference with non-public API, group-mapping and cleanup warnings; no upstream automation is bundled. The lifecycle skill is a governance/documentation surface, not another SAS execution client or permission to deploy.

Optional branches stay with those owners: decisioning adds MAS input/batch validation and Studio data-grid/lookup preparation; lifecycle adds Model Manager Fraud Ops KPI review; REST adds content-promotion mapping and SCR testing only when that runtime is explicitly selected. These references support offline plans without installing upstream applications or assuming Fraud Decisioning compatibility.

- “Use `sas-viya-decisioning-mcp` to preflight supplied MAS batch rows and Studio lookup inputs offline; do not score, import or activate.”
- “Use `sas-trustworthy-ai-lifecycle` to prepare a custom fraud KPI calculation and registration plan from synthetic mature-label evidence; do not run CAS or register it.”
- “Use `sas-viya-rest-api` to review a DEV-to-UAT content manifest offline, and load SCR testing only if our selected artifact uses SCR; do not authenticate, migrate or start containers.”

## Select and use

1. Read the selected skill's `SKILL.md` and check its scope.
2. Copy the **entire skill folder**, including `references/`, into the skills location supported by your agent host. Read that host's discovery documentation; this repository does not assume a global path, recursive discovery, or a particular invocation syntax.
3. Ask the agent for the actual task and provide available non-secret target details. The skill supplies procedure, not credentials or permission to change a server.

Each folder contains its own source/provenance references and works without siblings or repository-root documents. No installer, router, shared runtime, package installation, or suite activation is required to read these skills. Executing a SAS workflow still requires its actual client dependencies, authorized deployment access, and applicable SAS products/services.

For tutorial-based learning, the existing SWAT and SASPy folders include conditional guidance for the [SAS Viya Quick Start reference](SOURCES.md#supplementary-learning-references). No additional skill or sample-data installation is required to read it. Review its setup mutations and runtime assumptions before any authorized learning run; the examples are not production fraud policy.

Example requests:

- “Use `sasctl-python` to prepare model registration for our deployment; inspect installed versions and do not publish.”
- “Use `sas-viya-python-cas` to summarize an existing CAS table without downloading the entire table.”
- “Use `sas-viya-rest-api` to submit SAS code through Compute and verify the job's logs and output.”
- “Use `sas-viya-agentic-ai` to review a 2.4.1 model definition and explain deployment prerequisites without changing the site.”
- “Use `saspy-python` to prepare a Python-driven SAS reconciliation program for our Compute context; review logs and conversion checks without connecting yet.”
- “Use `sas-workbench-cli` to prepare a Workbench job launch and dependency checks; do not authenticate, schedule or submit yet.”
- “Use `sas-viya-decisioning-mcp` to inspect our exposed tools and review an existing decision's rules, revisions and lookup dependencies; do not compile, activate, publish or score.”
- “Use `sas-trustworthy-ai-lifecycle` to review our fraud model's release evidence and delayed-label monitoring plan; identify missing approvals without deploying or changing thresholds.”

These are invocation examples, not evidence that a particular host discovers the skills or that a live SAS workflow has been tested.

## Version policy

There is no single “SAS Viya version” that guarantees every integration works. SASPy can also target SAS 9.4, which is not a Viya release. Keep these facts separate:

- Deployment family: **Viya 3.5** versus **SAS Viya platform**.
- Platform release, **Stable/LTS** cadence where applicable, and relevant patches.
- Individual service API contract and request/response representation version.
- Python runtime and installed `sasctl`/`swat`/`saspy` versions, plus dependencies needed by the workflow.
- Workbench service capabilities and installed `wbctl` binary/build revision, separately from Viya and SAS engine versions.
- SAS MCP Server release/image, transport and discovered tool schemas/tier/filter policy, separately from the SAS product and platform versions.

Select release-matched official documentation and verify the target's capabilities before relying on an operation. An API catalog's highest version, a package badge, a documentation collection identifier, and today's date are not deployment compatibility evidence. Unknown facts must remain explicit.

The reference snapshot was researched on **2026-10-28**. Source versions describe what was consulted, not a promise of newest-release support. See [`SOURCES.md`](SOURCES.md) and the sources reference inside each package.

## Provenance and limitations

The skills contain independently authored procedural guidance informed by official SAS documentation and projects, plus an explicitly attributed **CC BY 4.0 adaptation** in the lifecycle skill's [evidence templates](skills/sas-trustworthy-ai-lifecycle/references/lifecycle-evidence.md). They do not bundle upstream implementation code or third-party binaries/media, and imply no SAS endorsement. Referenced materials retain their own terms. This repository declares no repository-wide license for its original skill texts; the adapted file's stated license is a specific exception, not a license for all other files.

Package structure, links, and local examples can be checked offline. Actual SAS authentication, permissions, CAS actions, model deployment, and Compute jobs require a live authorized environment; this repository does not claim those paths have been validated.
