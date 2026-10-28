---
name: sas-workbench-cli
description: "Use when orchestrating SAS Workbench jobs with wbctl, scheduling or observing job submissions, or managing Workbench environments and resources."
---

# SAS Workbench job orchestration

Use this skill as the coding agent or coordinating agent operating an authorized **SAS Workbench** installation through `wbctl`. It is an independent knowledge package, not a scheduler, an installed CLI, a new agent role or permission to run jobs. An ordinary Viya deployment is not automatically a Workbench endpoint. SASPy code execution, CAS actions and Fraud Decisioning service operations remain separate interfaces.

## 1. Establish target, versions and authority

Record the requested job/workflow, Workbench HTTPS endpoint, organization, profile, authenticated identity, dataplane, allowed resource IDs, permitted writes/executions, output destination and cost/concurrency bounds. Determine whether the task is local preparation, observation, job definition, one execution, recurring scheduling, cancellation or resource administration. Do not infer permission for the other stages.

Research baseline: upstream commit **`fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3`**, observed **2026-10-28**. Read [SOURCES.md](SOURCES.md) for pinned evidence and licensing. The changelog's `v0.1.0` heading is not an established published artifact: release/tag listings returned empty during research. Record the installed `wbctl version`, binary/build provenance, relevant command help and actual Workbench service capabilities independently. No client/server compatibility matrix is claimed. Do not install/upgrade merely to inspect; absent tools or target access do not prevent offline preparation.

## 2. Load the operation branch

- For endpoint/profile selection, login, token cache, noninteractive execution or wrong-identity errors, read [access and configuration](references/access-and-configuration.md) before contacting the server.
- For job definitions, submissions, schedules, monitoring, cancellation or coordinator logic, read [jobs and orchestration](references/jobs-and-orchestration.md).
- For workbench start/stop/create, image builds, volumes, snapshots or deletion, read [resource lifecycle](references/resource-lifecycle.md). Job permission does not grant infrastructure/storage administration.

## 3. Prepare the smallest operation

Resolve names to exact resources and verify owner, dataplane and current state. A **job** defines work; a **job submission** is an execution. Preserve the job ID separately from each submission ID and controller ID. Select application/services, command, inputs, context, volumes, environment and nodepool deliberately. Here a job `--context` means volume/snapshot/S3 context, not a Viya Compute context or Kubernetes context.

Review effects before execution: provider/API calls inside a job, data writes, mounted storage, schedules and environment variables can all exceed the apparent CLI operation. Prepare JSON against the installed/server-matched schema; do not invent fields or copy a different release's payload. Use documented dry-run for supported create operations, but do not claim it is offline, secret-safe or sufficient validation of server permissions/runtime behavior.

## 4. Execute, reconcile and release dependents

For an authorized execution, create at most the intended submission, retain its returned identity, then observe **that execution** to a documented terminal state with a bounded polling policy. Inspect sanitized diagnostics and required output artifacts/counts/schema or business assertions. Creation success, an exit-zero CLI process or empty logs do not establish successful job processing.

A coordinating agent owns dependency ordering, retries, deadlines and business-result checks; this skill does not establish native DAG or exactly-once guarantees. Release downstream work only after verified upstream completion and outputs. After a timeout or ambiguous create response, reconcile existing submissions and outputs before retrying. Unknown identity/state/schema means stop dependent actions, not infer success or submit another copy.

Cancellation, schedule changes and storage cleanup are distinct effects. Verify cancellation's final outcome and partial outputs; never treat it as rollback. Preserve evidence and task-owned resources until the required results are consumed, and do not delete volumes or rebuild images as generic error recovery.

## Result contract

Return: target/profile/organization and identity without secrets; binary/source/server evidence; exact job/submission/resource IDs and dataplane; operation and effects actually performed; schedule/concurrency policy if applicable; observed state and output validation; sanitized errors and unresolved outcomes; cost/cleanup status; and matching source URLs. When no target was exercised, label commands as prepared/source-checked, not successful Workbench execution.
