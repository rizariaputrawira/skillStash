# Content imports and upgrades — accelerator 2.4.1

Read before using `mdb builders-import`, deploying SAS Content or moving to another accelerator release. See the [tagged 2.4.1 changelog](https://github.com/sassoftware/sas-agentic-ai-accelerator/blob/2.4.1/CHANGELOG.md), [tagged repository](https://github.com/sassoftware/sas-agentic-ai-accelerator/tree/2.4.1), and official [transfer-package guide](https://sassoftware.github.io/sas-agentic-ai-accelerator/Administration-Guide/Setup-Additional-UIs). Hosted documentation is labeled current and can advance; use the changelog for exact upgrade deltas.

## Before an import or upgrade

1. Record deployed release/commit and target content root, Model Manager projects/models, Prompt Builder options, decisions using the Call LLM node, job definitions/contexts, CAS tables and local site-specific edits. Secure a recoverable copy through the site's approved change-management process.
2. Confirm SAS Viya meets the pinned release prerequisite (**2026.09+ for 2.4.1**) and required SAS/Visual Analytics products, report format support, `mdb`/Python CLI, SAS Viya CLI plugins, Model Manager permissions and Kubernetes/registry capability for the selected components.
3. Read all intervening changelog upgrade sections from the installed release to the destination release, in order. Apply only steps whose stated predicates match this site. Do not follow only the latest release's instructions where previous transitions have migrations.
4. Run the release's read-only/dry-run capability when available, examine captured option files and in-place writes, and identify possible overwrite/replacement/deletion, restarts, jobs and provider spend. Acquire authorization for each effect before mutation.

## Specific 2.4.1 upgrade note

The 2.4.1 changelog says sites with the Prompt Builder should update their checkout, run `mdb builders-import`, and reload the report. It also gives an **optional** step to remove `'unsafe-eval'` from the `script-src` CSP for SAS Job Execution **unless another page served there needs it**. Do not edit a site-wide CSP merely because the new builder no longer needs this capability; first check all consumers and follow SAS change control.

For earlier versions, read the exact changelog sections. Recent release notes describe separate conditional actions for RAG folders, model re-registration/republishing/restarts, credential-domain entry names, decision-node migration, content folder changes, and logging data/credentials. These are not all universally required for every installation. Choose by observed feature use and installed release; preserve content and historical data.

## Import and verify

For an authorized import, follow the 2.4.1 Model Definition Builder guide and inspect `mdb builders-import --help` first. The release documentation says it reads/captures Prompt Builder report options and writes package content in place; existing site options and objects can be replaced depending on path/type. Review captured options; do not use `--no-options-capture` as a shortcut if preserving site configuration matters.

A transfer import may involve reports/decisions separately from in-place files, steps and job definitions. The official docs warn that the *Call LLM* decision node has a shared ID: importing a node with that ID can overwrite local code/terms, and an older site's differently identified node can require a migration that rewrites decision versions. Never delete a node referenced by a decision. Follow the release-specific dry-run and migration; inspect every transfer job task, not only overall job state.

After a change, verify:

- Import command exit status, every transfer task, each intended created/updated/equal/skipped item, and the content `release.json`/commit marker.
- Prompt Builder report options restored to the intended repository/projects/endpoints/contexts/feature switches; reload the report.
- `Start Here` installation checks using a compute context that runs as the intended user; distinguish missing from not-checked due to permission/read failures.
- Credential domain and identities, CAS tables, Model Manager projects, job definitions and contexts, publishing destinations, and Python packages required for enabled features.
- Existing decisions still resolve the expected node ID; validate current and locked versions where the release migration affects them.
- Runtime deploys only if separately authorized: verify rollout, target pod/container configuration, network exposure, TLS, and synthetic call result.

A completed transfer-job status does not prove every task completed. Preserve command output and sanitized job/task IDs. If a request timed out, inspect target state before repeating an import or write.
