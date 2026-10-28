# Workbench environments, images and storage

Load only when the assignment includes resource administration. Use the installed command-specific help and the [pinned command catalog](https://github.com/sassoftware/wbctl/tree/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/doc). This branch is not required for observing an existing job and does not grant Kubernetes/cluster administration.

## Inspect before mutation

Bind endpoint/profile/organization and identity as described in [access](access-and-configuration.md). Resolve resource IDs, owner, dataplane and current state; inventory dependencies and consumers before changing shared resources. Verify application/service availability and nodepool/storage capabilities from the actual Workbench installation, not sample README IDs.

| Requested effect | Required boundaries and proof |
| --- | --- |
| Create/start a workbench | Approved application/services, dataplane, nodepool, volumes, resource/cost limits; inspect desired versus actual state, service readiness and intended access |
| Stop a workbench | Confirm the exact environment and active work/users/jobs; establish persistence policy before interruption; verify actual stopped state rather than only command acceptance |
| Create/rebuild an image | Review build context, source revision, dependencies, secrets and consumers; verify build completion, safe logs and resulting image identity before adoption |
| Create/attach a volume | Confirm storage class, capacity, owner/access policy and mount identity/path; inspect existing content/consumers before reuse; attachment alone does not validate permissions or durability |
| Create/use a snapshot | Confirm source volume and consistency expectations; observe completion and establish a tested restore procedure; snapshot existence alone is not recoverability proof |
| Edit/update/delete a resource | Inspect current representation and dependencies, minimal requested change and available concurrency protection; verify post-change state and preserve unrelated resources |

These are workflow checks, not promises about every command's flags, persistence behavior or API guarantees. Verify each with matching help/source and deployment documentation before execution. Use JSON from the matching schema; do not clone a live object's server-managed fields into a guessed create payload.

## Storage and destructive operations

Volumes, snapshots, workbenches, jobs and submissions are distinct resources. Deleting a submission record is not the same as cancelling execution; stopping an environment is not the same as deleting its storage. Determine the actual cascade/retention semantics before action; do not infer them from kubectl-like names or aliases.

Before deletion, show exact IDs, owner/dataplane, affected consumers, expected data loss and approved recovery/retention plan. Obtain task authorization for the destructive effect. Do not assume an interactive confirmation prompt or global dry-run exists: the inspected volume-delete help lists no such safeguards. Avoid broad selection, name-only deletion and cleanup of shared data. A pending/failed job is not authorization to delete its mounted volume.

Dry-run support is command-specific. Even a supported create dry-run may authenticate/contact the server and print sensitive payload fields. Verify behavior before using it as a safety boundary. Do not copy production environment values into exported definitions, dry-run reports or troubleshooting output.

After an authorized operation, verify the exact resource state, remaining dependents and preserved outputs. Retain failed build/execution evidence until diagnosis and retention requirements are met; remove only task-owned temporary resources when cleanup is within scope. Report unverified cascade, persistence or restore behavior explicitly.
