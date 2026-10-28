---
name: sas-viya-agentic-ai
description: "Use when working with SAS Agentic AI Accelerator 2.4.1: Model Definition Builder, Prompt Builder, RAG, credentials, content upgrades, or deployment."
---

# Work with SAS Agentic AI Accelerator

This skill is for an agent helping an administrator or developer use the SAS-maintained accelerator. It is **not** a general agent-building recipe or permission to deploy into SAS Viya/Kubernetes. Work against the release actually installed or approved for the task; do not mix commands, generated assets, documentation, or migrations from different releases.

## Compatibility gate

The research baseline is release tag **2.4.1** (released 2026-10-27). The matching tagged CLI metadata sets `sas-mdb`'s Python requirement to **3.11 or newer**. The release documentation's administration prerequisite is **SAS Viya 2026.09 or later**; its transferred Visual Analytics reports use BIRD 4.68 and may not open on earlier releases. Treat that as a compatibility gate for this release, not a guarantee that any deployment at 2026.09 supports every optional component.

Before advising code or commands, establish:

1. Installed accelerator tag/commit, Viya platform release/update, selected `mdb` version and Python interpreter. Distinguish them. A checkout branch or mutable `main` URL alone is not the installed/deployed version.
2. Which feature is in scope: local definition creation, provider call, Model Manager registration, container publishing/deployment, prompt authoring, decision integration, RAG ingestion/retrieval, content import, or upgrade.
3. Which environment the action targets and who owns each permission: SAS Administrator, Kubernetes/cluster administrator, model administrator, or ordinary user. Identify exact effects and whether a live write is authorized.
4. The release-matched instructions and migration steps. Read the conditional reference before acting:
   - For `mdb add/generate/test/validate/register/publish/ship` or a model-definition change, read [Model Definition Builder 2.4.1](references/model-definition-builder.md).
   - For provider keys, auth, container exposure, TLS, or MAS/SCR runtime configuration, read [Credentials and deployment boundaries](references/credentials-and-deployment.md).
   - For Prompt Builder, transfer-package content, `builders-import`, or an upgrade, read [Content and upgrades](references/content-and-upgrades.md).
   - For the exact observed release, license/provenance, and source URLs, read [Sources and license boundaries](references/sources.md).

If the installed accelerator differs from 2.4.1, use that release's tagged changelog and matching docs instead. Never silently apply 2.4.1 commands to an older checkout. If target requirements do not match the compatibility gate, stop before installation/import/deployment and report the mismatch; continue only with clearly separated offline analysis that does not claim support.

## Work sequence

1. **Inspect before writing.** Check the existing definition, manifest, package and site configuration using the target release's supported tooling. Keep definitions and assets in the intended repository. Do not overwrite local options, generated work, model versions, content objects or site configuration without checking their owner and the upgrade contract.
2. **Prefer manifest-driven work.** For a supported model, let the pinned `mdb` create/update definition assets; review the diff, key handling, provider parameters, input/output contract, requirements and model card. Preserve manual changes using documented overrides. Validate model options against the provider contract and record unknown pricing/limits instead of inventing them.
3. **Separate validation layers.** `mdb validate`/`--live`, `mdb test`, registration, publishing and runtime invocation prove different things. A local provider call is not a test of generated score code; successful registration is not publication; an accepted publication request is not a healthy inference service. Report exactly which stage ran and what evidence it returned.
4. **Guard mutations and spend.** Provider smoke tests can incur charges. Registration/publishing changes Model Manager and may start builds; applying a rendered Deployment changes Kubernetes. Content imports can overwrite shipped objects in place, including the shared Call LLM node at the release-defined ID. Review the exact target, dry run where supported, expected deletions/replacements, restart/rollout effects, and authorization before mutation. Never blindly retry an ambiguous write.
5. **Validate the result where it runs.** Re-read the Model Manager model/version or destination object, inspect build/job state and safe logs, verify the actual container's readiness and required configuration, then call a bounded authorized test. For content upgrades, inspect import task results and the release marker, then follow Start Here checks. Do not treat a completed transfer job as proof every task succeeded.
6. **Report with version boundaries.** State accelerator tag, Viya release, Python/`mdb` versions, feature/provider/runtime and deployment type; list operations actually run, effects, model/resource identity, sanitized result, and remaining unknowns. Keep credentials, raw prompts containing sensitive data, provider responses, tokens and secrets out of reports.

## Security boundary

The accelerator supplies useful governance integrations but does not itself prove that a deployed model endpoint is authenticated, protected from network callers, or safe to use autonomously. The release documentation explicitly notes that generated ingress may have no authentication. Verify access controls and secret delivery on the actual deployment before exposing a provider-key-backed endpoint. For RAG, validate document-level authorization, ingestion scope, retrieval filters, data retention and downstream prompt logging under the deployment's policy.

Use TLS verification by default. Never change certificate checks, grant group-wide credentials, widen network access, create OAuth clients, import site content, replace a published model, run an ingestion, or patch/restart Kubernetes components just to suppress an error. These effects require explicit task authorization and exact target identity.

## Result contract

Return: installed/researched versions and sources; exact workflow stage; files/resources changed or command prepared; authorization and side effects; observed validation state; security/configuration checks; unsupported or unverified conditions; and next required administrator action. When no target exists, finish local review and state that Viya behavior was not exercised.
