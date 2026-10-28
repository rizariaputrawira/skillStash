# Skillstash

**Standalone skills for SAS work.**

Optional AI-agent guidance for SAS and SAS Viya: choose the skills you need for service integrations, data processing, decisioning or model governance. Each folder works independently of KitShift, Leanflow and the other skills in this repository.

This is a skill collection, not a coding-agent runtime, workflow suite or Git cherry-pick mechanism. It does not install SAS clients, connect services or grant deployment access.

## In this guide

- [Capabilities](#capabilities)
- [Quick start](#quick-start)
- [How it works](#how-it-works)
- [Principles](#principles)
- [Skills and workflows](#skills-and-workflows)
- [Safety and privacy](#safety-and-privacy)
- [Updates and troubleshooting](#updates-and-troubleshooting)
- [Verification and known limits](#verification-and-known-limits)
- [Help and maintenance](#help-and-maintenance)
- [Documentation, sources, and licenses](#documentation-sources-and-licenses)

## Capabilities

- **SAS integrations:** procedures for `sasctl`, SWAT, SASPy, REST and Workbench, with distinct client and runtime boundaries.
- **Decisioning:** MCP-aware asset review, lookup versions, MAS publication and business-result verification, subject to actual product support.
- **Model governance:** evidence templates, fraud-model review and monitoring plans, with approval kept separate from execution.
- **Independent selection:** eight self-contained packages with small entrypoints, conditional references and local provenance records.

Skills supply procedures, not guarantees of agent compliance, product compatibility or successful SAS execution.

## Quick start

### 1. Get the collection

Clone the repository or download the skill folder you want:

```sh
git clone https://github.com/rizariaputrawira/skillstash.git
cd skillstash
```

No package installation or language runtime is required to read the skills. Executing a SAS workflow still requires its actual client dependencies, authorized deployment access and applicable SAS products/services.

### 2. Select a skill

Use the [skill collection](#skill-collection) to choose the execution or review surface, then read that folder's `SKILL.md`. For example, select `sas-viya-python-cas` for server-side CAS processing, not SASPy or a generic REST workflow.

Copy the **entire skill folder**, including `references/` and its source record, into the skills location supported by your agent host. Read that host's discovery documentation; this repository does not assume a global path, recursive discovery or a particular invocation syntax.

### 3. Ask for the task

Name the skill and supply available non-secret target details and permitted effects:

> Use `sas-viya-decisioning-mcp` to review supplied MAS batch inputs offline; do not connect, publish or score.

The host supplies the agent and tools. The skill supplies procedure, not credentials or permission to change a server.

## How it works

The selected `SKILL.md` defines the core workflow and points to specialized references only when the task needs them. Each package contains its own source/provenance record and works without siblings or repository-root documents. There is no installer, router, shared runtime or suite activation.

Choose by execution surface: `sasctl` integrates with Viya services; SWAT runs CAS actions; SASPy runs SAS code through a SAS session, including Viya Compute or SAS 9.4 Workspace Server; `wbctl` controls a separate SAS Workbench installation and its jobs/resources; the accelerator adds its own Model Definition Builder and content workflows. These are not interchangeable clients or runtimes. REST guidance also applies outside Python.

### MCP integrations

For agent-operated decisioning work, inspect the tools of an already configured SAS MCP server before rebuilding REST orchestration. The skill supplies procedure; the MCP server supplies executable tools. Tool exposure, server version, Viya permissions and the exact SAS product determine what is available. No MCP server is installed or connected by selecting a skill.

## Principles

Inspired by [Matt Pocock's skills collection](https://github.com/mattpocock/skills): individually selectable guidance, not a framework that owns the user's workflow.

- **One distinct task per skill.** Create a standalone skill for a genuinely different trigger; extend an existing reference when the task already has an owner.
- **Independent selection.** Keep each folder self-contained, with no Kitshift, Leanflow or sibling-skill requirement.
- **Small entrypoints, conditional detail.** Put the core procedure in `SKILL.md`; load specialized references and templates only when relevant.
- **Actionable guidance.** Turn supported source evidence into usable procedures; an upstream repository does not automatically warrant a new skill.
- **User-controlled effects.** Selecting a skill does not authorize installation, authentication, deployment or administrative changes.
- **Version-aware and license-aware.** Distinguish consulted source revisions from target compatibility; preserve attribution and file-specific license boundaries.
- **No framework by imitation.** Add setup, plugins or distribution machinery only for an actual user need, not to mirror another collection.

## Skills and workflows

Ask for the actual task, or name a skill when you want its procedure. Selection is by task and execution surface, not by installing a complete suite.

### Skill naming

Canonical names use `<domain>-<capability>` in lowercase kebab-case. Use `sas-` for SAS-specific skills, with product/client qualifiers where useful, such as `sas-viya-rest-api` or `sas-saspy-python`. The prefix describes the task domain, not the upstream author or repository.

For future non-SAS skills, choose a descriptive domain such as `data-`, `cloud-` or `finance-`; do not force unrelated guidance under `sas-`. A portable, vendor-neutral capability should use its own domain. These are naming examples, not additional packages. Directory names and `SKILL.md` frontmatter names must match.

### Skill collection

| Skill | Select it when you need |
| --- | --- |
| [`sas-sasctl-python`](skills/sas-sasctl-python/SKILL.md) | Python integration through `sasctl`: service calls, model registration, packaging, publishing, and outcome verification |
| [`sas-viya-python-cas`](skills/sas-viya-python-cas/SKILL.md) | Python SWAT workflows: CAS connections/actions, server-side data processing, and table/session lifecycle |
| [`sas-viya-rest-api`](skills/sas-viya-rest-api/SKILL.md) | Language-independent REST integrations, OAuth, API contracts, and asynchronous SAS Compute/Job Execution |
| [`sas-viya-agentic-ai`](skills/sas-viya-agentic-ai/SKILL.md) | SAS Agentic AI Accelerator: versioned Model Definition Builder, prompt/content workflows, credentials, RAG, deployment and upgrade boundaries |
| [`sas-saspy-python`](skills/sas-saspy-python/SKILL.md) | Run SAS programs from Python through Viya Compute or SAS 9.4 sessions; validate logs, dataset transfers and session cleanup |
| [`sas-workbench-cli`](skills/sas-workbench-cli/SKILL.md) | Orchestrate SAS Workbench job definitions, schedules and executions with `wbctl`; verify outcomes and manage environments/storage only when authorized |
| [`sas-viya-decisioning-mcp`](skills/sas-viya-decisioning-mcp/SKILL.md) | Use discovered SAS MCP tools for supported decisioning assets, rules, lookup versions, MAS publication and business-result verification; check exact Fraud Decisioning applicability |
| [`sas-trustworthy-ai-lifecycle`](skills/sas-trustworthy-ai-lifecycle/SKILL.md) | Prepare model-governance evidence, production-readiness reviews and monitoring plans, with fraud-specific review guidance and separate owner approval |

### Example requests

- “Use `sas-sasctl-python` to prepare model registration for our deployment; inspect installed versions and do not publish.”
- “Use `sas-viya-python-cas` to summarize an existing CAS table without downloading the entire table.”
- “Use `sas-viya-rest-api` to submit SAS code through Compute and verify the job's logs and output.”
- “Use `sas-viya-agentic-ai` to review a 2.4.1 model definition and explain deployment prerequisites without changing the site.”
- “Use `sas-saspy-python` to prepare a Python-driven SAS reconciliation program for our Compute context; review logs and conversion checks without connecting yet.”
- “Use `sas-workbench-cli` to prepare a Workbench job launch and dependency checks; do not authenticate, schedule or submit yet.”
- “Use `sas-viya-decisioning-mcp` to inspect our exposed tools and review an existing decision's rules, revisions and lookup dependencies; do not compile, activate, publish or score.”
- “Use `sas-trustworthy-ai-lifecycle` to review our fraud model's release evidence and delayed-label monitoring plan; identify missing approvals without deploying or changing thresholds.”

### Optional reference branches

These stay within existing owners, without requiring upstream application installation or assuming Fraud Decisioning compatibility:

- **Decisioning:** MAS input/batch validation and Studio data-grid/lookup preparation. Example: “Use `sas-viya-decisioning-mcp` to preflight supplied MAS batch rows and Studio lookup inputs offline; do not score, import or activate.”
- **Lifecycle:** Model Manager Fraud Ops KPI review. Example: “Use `sas-trustworthy-ai-lifecycle` to prepare a custom fraud KPI calculation and registration plan from synthetic mature-label evidence; do not run CAS or register it.”
- **REST:** content-promotion mapping and SCR testing only when that runtime is explicitly selected. Example: “Use `sas-viya-rest-api` to review a DEV-to-UAT content manifest offline, and load SCR testing only if our selected artifact uses SCR; do not authenticate, migrate or start containers.”

For Studio step-access administration, the REST skill contains a conditional reference with non-public API, group-mapping and cleanup warnings; no upstream automation is bundled. The lifecycle skill is a governance/documentation surface, not another SAS execution client or permission to deploy.

For tutorial-based learning, the existing SWAT and SASPy folders include conditional guidance for the [SAS Viya Quick Start reference](SOURCES.md#supplementary-learning-references). No additional skill or sample-data installation is required to read it. Review its setup mutations and runtime assumptions before any authorized learning run; the examples are not production fraud policy.

## Safety and privacy

Selecting or naming a skill does not authorize installation, authentication, code execution, publication, lookup activation, content migration or administrative changes. Bind the intended environment, identity, resources and permitted effects before live work. Do not use another client or transport to bypass deliberate MCP filtering or a permission denial.

Keep credentials, tokens and raw customer/transaction data out of prompts, examples, diagnostics and issue reports. Use approved synthetic cases for offline preparation; reconcile partial or uncertain effects before replaying a write or scoring request.

## Updates and troubleshooting

Update the checkout, review the changed skill and its local source record, then deliberately replace the copied package in your host's skills location. There is no managed updater or automatic synchronization; preserve local edits before replacing a copy.

If a skill is not discovered, check the host's documented skill location, metadata requirements and loading interface. If a SAS operation is unavailable, distinguish client/tool exposure, target product/release support and actual permissions. Installing more skills does not resolve those deployment facts.

### Version policy

There is no single “SAS Viya version” that guarantees every integration works. SASPy can also target SAS 9.4, which is not a Viya release. Keep these facts separate:

- Deployment family: **Viya 3.5** versus **SAS Viya platform**.
- Platform release, **Stable/LTS** cadence where applicable, and relevant patches.
- Individual service API contract and request/response representation version.
- Python runtime and installed `sasctl`/`swat`/`saspy` versions, plus dependencies needed by the workflow.
- Workbench service capabilities and installed `wbctl` binary/build revision, separately from Viya and SAS engine versions.
- SAS MCP Server release/image, transport and discovered tool schemas/tier/filter policy, separately from the SAS product and platform versions.

Select release-matched official documentation and verify the target's capabilities before relying on an operation. An API catalog's highest version, a package badge, a documentation collection identifier, and today's date are not deployment compatibility evidence. Unknown facts must remain explicit.

The reference snapshot was researched on **2026-10-28**. Source versions describe what was consulted, not a promise of newest-release support. See [SOURCES.md](SOURCES.md) and the sources reference inside each package.

## Verification and known limits

Package structure, frontmatter, relative links and local examples can be checked offline. Source-loaded synthetic exercises can check how an agent follows a branch, but do not establish a universal behavior guarantee.

**Not established by those checks:** native host discovery, SAS authentication, permissions, deployed-product compatibility, CAS actions, model deployment, Compute jobs or end-to-end Fraud Decisioning behavior. Live workflows require an authorized environment. The invocation examples above are not evidence that a host discovers the skills or that a live SAS workflow has been tested.

There is no repository-level application build, test runner or SAS runtime. Contributor checks and evidence-reporting guidance are in [AGENTS.md](AGENTS.md#testing--qa).

## Help and maintenance

For collection problems or suggestions, [open an issue](https://github.com/rizariaputrawira/skillstash/issues) with the skill name, relevant file path, expected behavior and sanitized evidence. Include host/client/product versions when they affect the problem; never attach tokens, authentication stores or raw customer data.

For host discovery or SAS deployment problems, consult the relevant host documentation and target-matched SAS documentation. This collection does not supply product access or a blanket support matrix.

## Documentation, sources, and licenses

- [Repository guidelines](AGENTS.md): architecture, authoring conventions and offline QA.
- [Sources and provenance](SOURCES.md): source/version index, incorporation limits and license observations.
- [Skill packages](skills/): entrypoints, conditional references and package-local detailed source records.

The skills contain independently authored procedural guidance informed by official SAS documentation and projects, plus an explicitly attributed **CC BY 4.0 adaptation** in the lifecycle skill's [evidence templates](skills/sas-trustworthy-ai-lifecycle/references/lifecycle-evidence.md). They do not bundle upstream implementation code or third-party binaries/media, and imply no SAS endorsement. Referenced materials retain their own terms.

This repository declares **no repository-wide license** for its original skill texts. The adapted file's stated license is a specific exception, not a license for all other files. Consult the package-local source record before copying or adapting additional upstream material.
