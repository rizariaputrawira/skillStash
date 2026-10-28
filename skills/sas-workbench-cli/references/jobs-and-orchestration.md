# Job definitions, executions and coordinator control

Use [pinned create-job help](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/doc/wbctl%20create%20job.md), [submission creation](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/internal/cmd/create/job_submission/job_submission.go) and the installed command help. Read [access](access-and-configuration.md) before authenticated operations.

## Define versus run

| Operation | Meaning / required check |
| --- | --- |
| `create job` | Create a definition with application or services, command, optional context, mounts/environment and schedule; not proof of a completed execution |
| `create job-submission --job-id ...` | Create an execution of an existing job; retain the returned submission ID |
| `start jobs ...` | The pinned implementation routes this to submission creation; do not interpret it as harmlessly resuming the same existing execution |
| `get job-submissions ... --output json` | Retrieve execution records; validate the actual output shape/identities before parsing |
| `describe job-submissions ...` | Human-facing details, including environment and properties; inspect only in an approved private surface |
| `logs job-submissions ...` | Execution diagnostics; logs are not a success criterion and can contain sensitive data |
| `cancel job-submission ...` | Request cancellation for selected execution IDs; verify final state and partial outputs separately |

### Prepare a job

Resolve the intended existing job first. For a new authorized definition, select a known application or services, reviewed command, exact dataplane, nodepool/resource policy, input context, mount identities and output paths. `--context` accepts a volume, snapshot or S3 bucket; verify the matching access/path semantics and any `--sub-path`. Do not assume a local checkout or Python environment exists inside the job.

For a recurring schedule, establish the server's cron syntax, timezone, overlap policy, missed-run behavior and owner before enabling it. The source help documents a cron `--schedule`; it does not establish those semantics or a DAG scheduler. With no schedule, the help says a job runs when a submission is explicitly created. Do not schedule and also launch manually unless both effects are intended.

### Review a submission before sending

This original shell example assumes an approved existing job and non-secret target IDs supplied in environment variables. It is **not offline**: pinned code authenticates and resolves dataplane/volume information before checking `--dry-run`. It suppresses the create request, not all network/cache activity. Review printed payloads privately.

```bash
: "${WB_PROFILE:?Select an approved Workbench profile}"
: "${WB_DATAPLANE_ID:?Supply the intended dataplane ID}"
: "${WB_JOB_ID:?Supply the reviewed existing job ID}"
wbctl --profile "$WB_PROFILE" create job-submission \
  --job-id "$WB_JOB_ID" --dataplane "$WB_DATAPLANE_ID" --dry-run
```

Confirm the payload identifies the reviewed job/dataplane, expected mounts/environment and resource placement. A dry-run is not proof of server acceptance, execution readiness or business correctness. Remove the dry-run flag only for an authorized launch, capture the returned ID securely, and preserve it with the logical workflow/run identity. The baseline create path prints an ID, not a completed job result; do not parse it as a status JSON document.

## Observe an exact execution

For an already known submission, this example performs a server read; it is not a polling loop or an execution trigger:

```bash
: "${WB_PROFILE:?Select an approved Workbench profile}"
: "${WB_SUBMISSION_ID:?Supply the submission ID returned by creation}"
wbctl --profile "$WB_PROFILE" get job-submissions "$WB_SUBMISSION_ID" --output json
```

The pinned service exposes current state, message, runtime, job name, submission/controller IDs and log URL in its representations. Do not infer machine JSON keys from table headings: inspect the matching type/schema and actual sanitized response first. The source query can match IDs or controller IDs, so verify every returned record's exact identity; a selector may not mean one unique execution. Do not dump unrelated submissions when an exact ID suffices.

Define polling interval, total deadline and supported terminal-state meanings from target evidence; do not invent a `--wait`/`--watch` flag or hard-code guessed state strings. Distinguish transient communication failures from terminal execution failure. Unknown/unreadable state blocks downstream steps. Restrict log retrieval to the intended submission and sanitize before sharing; do not automatically follow arbitrary returned log URLs with bearer credentials.

Completion requires both a documented successful terminal state and expected outputs: destination identity, schema/counts/reconciliation and any application/SAS diagnostics. CLI exit zero may mean only that creation/read/cancel succeeded. A job's script must propagate relevant application failures; a successful shell wrapper does not prove SAS/business checks passed.

## Coordinator responsibilities

For each logical run, retain target/profile/organization, job definition identity, input/version or snapshot identity, schedule/manual trigger, returned submission IDs, current observed state, required output checks and allowed effects. Keep this mapping in the caller's existing workflow/run record, not a new mandatory shared service.

- **Dependencies:** release B only after A's intended execution and outputs are verified. Workbench job creation is not evidence of native dependency ordering.
- **Concurrency:** set explicit launch bounds and handle schedule/manual overlap and shared-volume/output collisions. A pre-launch lookup alone is not an atomic lock or exactly-once guarantee.
- **Retries:** distinguish read/poll retries from creating another execution. After an ambiguous create response, reconcile server records and outputs using available identifiers and caller run metadata. Labels can help correlation but are not an idempotency guarantee. If identity cannot be established, stop and report uncertainty rather than duplicating an effectful job.
- **Cancellation/deadlines:** cancel only the authorized submission, observe the resulting state, and identify partial outputs. Cancellation or a coordinator timeout does not undo writes or necessarily disable future scheduled runs.
- **Recovery:** preserve logs/outputs and resolve the cause before an approved retry; do not delete/recreate jobs, reset volumes, or change identity as generic recovery. If a retry is authorized, record it as a new attempt linked to the prior outcome.
