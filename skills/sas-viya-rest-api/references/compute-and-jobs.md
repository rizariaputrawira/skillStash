# Execute SAS code and verify asynchronous jobs

Read for SAS Compute sessions or Job Execution submissions. These are different contracts, not interchangeable paths to the same job object. SWAT CAS actions are a third interface; a CAS connection does not provide a Compute session.

## Choose the service

| Need | Contract to inspect |
| --- | --- |
| Submit SAS code into a Compute session; inspect session data/log/listing | [Compute](https://developer.sas.com/rest-apis/compute-v24/index.md), using the version supported by the target |
| Execute/manage a job request through a configured provider | [Job Execution](https://developer.sas.com/rest-apis/jobExecution-v7/index.md), plus that provider's contract |
| Create/manage reusable job definitions | Job Definitions, selected from the [catalog](https://developer.sas.com/rest-apis) |

The v24/v7 links are researched examples, not minimum requirements or recommendations to upgrade.

## Compute lifecycle

1. List permitted contexts and resolve the intended context ID unambiguously. Inspect its launch requirements; do not create or alter a shared context merely because session launch fails.
2. Create a session only when authorized. Wait for the documented usable state, retaining its ID, returned links, and ownership. Decide whether the session is borrowed or created for this operation; cleanup must not terminate someone else's session.
3. Submit code using the target-supported `createJob` operation. In [Compute v24](https://developer.sas.com/rest-apis/compute-v24/createJob/index.md), the runtime route is `POST /compute/sessions/{sessionId}/jobs`; the request carries `code` as an array of strings or a Files-service `codeUri`. The request representation schema describes version 3, distinct from API version 24. Build the actual payload from the selected contract rather than assuming that number applies to older deployments.
4. Preserve the job resource identity and Location/response links. v24 returns **201 Created**, while code execution is always asynchronous. Do not use `status == 202` as the only submission-success test or treat 201 as completed execution.
5. Read the job state using its advertised link. The [v24 state operation](https://developer.sas.com/rest-apis/compute-v24/getJobState/index.md) returns `text/plain`: `pending`, `running`, `completed`, `warning`, `error`, or `canceled`. Poll with a bounded deadline and documented pacing/conditional requests. Its `wait` query parameter requires `If-None-Match`; handle 304 as unchanged state, not JSON content. Re-check semantics for another service/version.
6. At terminal state, inspect the job object, condition code (`jobConditionCode`, based on SAS `SYSCC` in this contract), relevant log errors/warnings, and actual expected output. A completed HTTP exchange or available listing is insufficient. Decide whether warnings satisfy the requested task rather than silently counting them as success.
7. Retrieve needed logs/listing/results before ending an owned session; redact sensitive records and any embedded credentials. Validate expected table/columns or a bounded output summary, not only a job status string.
8. Clean up only resources created for the task and only within authorization. A polling timeout leaves the remote job outcome unknown; report the job/session IDs and reconcile before resubmitting. Cancellation, deletion and closing a session have different effects; use the contract rather than assuming one implies another.

## Job Execution differences

From the [v7 index](https://developer.sas.com/rest-apis/jobExecution-v7/index.md), jobs, job requests, provider discovery, and job-state operations belong to Job Execution. Check the chosen provider's request arguments, identity requirements, result/log links and retention policy. Do not copy Compute's session route, state enum, representation version, or condition-code field into a Job Execution client. If a request requires heartbeats, implement the documented lifecycle; the v7 index lists a heartbeat-timeout failure. Do not assume every provider needs the same heartbeat behavior.

## Minimal proof to retain

Sanitized context/provider identity, owned versus borrowed session, submitted job ID, final state, condition/error evidence, expected output verification, and cleanup outcome. Without a deployment, validate the selected schemas and control flow only; mark execution, permissions and outputs unverified.

Official operation/index sources above consulted 2026-10-28. No live jobs were run to author this reference.
