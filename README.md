# Skillstash

Optional, standalone AI-agent skills to select individually—not a workflow suite or Git cherry-pick mechanism. Independent of Kitshift and Leanflow.

## SAS Viya skills

| Skill | Select it when you need |
| --- | --- |
| [`sasctl-python`](skills/sasctl-python/SKILL.md) | Python integration through `sasctl`: service calls, model registration, packaging, publishing, and outcome verification |
| [`sas-viya-python-cas`](skills/sas-viya-python-cas/SKILL.md) | Python SWAT workflows: CAS connections/actions, server-side data processing, and table/session lifecycle |
| [`sas-viya-rest-api`](skills/sas-viya-rest-api/SKILL.md) | Language-independent REST integrations, OAuth, API contracts, and asynchronous SAS Compute/Job Execution |

Choose by execution surface: `sasctl` integrates with Viya services; SWAT runs CAS actions; Compute executes SAS code in a Compute session. These are not interchangeable clients or runtimes. REST guidance also applies outside Python.

## Select and use

1. Read the selected skill's `SKILL.md` and check its scope.
2. Copy the **entire skill folder**, including `references/`, into the skills location supported by your agent host. Read that host's discovery documentation; this repository does not assume a global path, recursive discovery, or a particular invocation syntax.
3. Ask the agent for the actual task and provide available non-secret target details. The skill supplies procedure, not credentials or permission to change a server.

Each folder contains its own source/provenance references and works without siblings or repository-root documents. No installer, router, shared runtime, package installation, or suite activation is required to read these skills. Executing a SAS workflow still requires its actual client dependencies, authorized deployment access, and applicable SAS products/services.

Example requests:

- “Use `sasctl-python` to prepare model registration for our deployment; inspect installed versions and do not publish.”
- “Use `sas-viya-python-cas` to summarize an existing CAS table without downloading the entire table.”
- “Use `sas-viya-rest-api` to submit SAS code through Compute and verify the job's logs and output.”

These are invocation examples, not evidence that a particular host discovers the skills or that a live SAS workflow has been tested.

## Version policy

There is no single “SAS Viya version” that guarantees every integration works. Keep these facts separate:

- Deployment family: **Viya 3.5** versus **SAS Viya platform**.
- Platform release, **Stable/LTS** cadence where applicable, and relevant patches.
- Individual service API contract and request/response representation version.
- Python runtime and installed `sasctl`/`swat` versions, plus dependencies needed by the workflow.

Select release-matched official documentation and verify the target's capabilities before relying on an operation. An API catalog's highest version, a package badge, a documentation collection identifier, and today's date are not deployment compatibility evidence. Unknown facts must remain explicit.

The reference snapshot was researched on **2026-10-28**. Source versions describe what was consulted, not a promise of newest-release support. See [`SOURCES.md`](SOURCES.md) and the sources reference inside each package.

## Provenance and limitations

The skills are original procedural guidance informed by official SAS documentation and projects; they do not bundle upstream implementation code or imply SAS endorsement. Referenced materials retain their original terms. No repository-wide license is declared here; do not assume referenced projects' licenses apply to these skill texts.

Package structure, links, and local examples can be checked offline. Actual SAS authentication, permissions, CAS actions, model deployment, and Compute jobs require a live authorized environment; this repository does not claim those paths have been validated.
