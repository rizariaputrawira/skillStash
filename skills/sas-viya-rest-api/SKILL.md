---
name: sas-viya-rest-api
description: "Use when implementing or troubleshooting SAS Viya REST integrations, OAuth, service contracts, or asynchronous SAS Compute and Job Execution workflows."
---

# SAS Viya REST integrations

For an agent building or diagnosing an authorized integration, not permission to administer a deployment. Works independently of any skill suite or Python client. Deliver the requested client or diagnosis with contract evidence and observed results; never invent endpoints from service names.

## Workflow

1. **Bind the target.** Record deployment family (Viya 3.5 or SAS Viya platform), release and Stable/LTS cadence where applicable, relevant patch information, base URL, service, caller identity type, and permitted effects. Read [version selection](references/versioning.md) before choosing a contract. Resolve facts from authorized deployment records or the administrator; unknown release information blocks a compatibility claim, not offline implementation.
2. **Find the actual service contract.** Start with the [official API catalog](https://developer.sas.com/rest-apis); open the selected service's versioned index and linked OpenAPI. Match operations to target-supported documentation and advertised resource links. Record the contract URL/version. A public catalog entry is not evidence of installed capability. Take service prefixes from the specification, replacing example server hosts with the authorized target; the documentation site's paths are not runtime routes.
3. **Establish authentication.** For new clients, grant selection, or token errors, read [authentication and requests](references/auth-and-requests.md). Use an existing approved client/credential source; registering clients or changing authorities is a separate administrative action. Keep tokens, credentials, cookies, and authorization headers out of source, shell history, traces, notebooks, and reports. Keep certificate verification enabled; configure the approved trust chain instead of copying insecure example flags.
4. **Implement the documented exchange.** Match method, path, request representation, Accept/Content-Type, required fields, query encoding, and response schema to that operation. Treat API version, representation `version`, and media-type versions as distinct. Use IDs/URIs, not ambiguous display names. For collections, follow documented paging and filtering; an initial page is not a complete search. Follow links only within the approved HTTPS origin or an explicitly authorized destination; never forward bearer credentials to an arbitrary returned URL.
5. **Handle effects and completion.** Identify mutations by semantics, not HTTP verb alone: some POST operations retrieve data, others submit jobs. Confirm exact targets before changes; use concurrency preconditions when required. Do not replay a submission after a timeout until its outcome is reconciled. For SAS code execution, session ownership, polling, logs, and job outputs, read [Compute and jobs](references/compute-and-jobs.md). An HTTP success or job resource creation is not analytical success.
6. **Exercise and report.** On an authorized environment, run the smallest relevant operation and verify resource identity, final job state and actual output as appropriate. Otherwise validate request construction against the selected contract without sending it; state that live behavior is unverified. Report target release/cadence, service contract, auth mechanism (no values), sanitized status/error evidence, resulting resources, and unknowns separately.

## Diagnostic boundaries

- `401`: inspect token validity, issuer/audience and supported flow; do not print the token or assume a password retry is valid.
- `403`: inspect caller permissions, client authorities and resource authorization; a valid token does not confer all access.
- `404`: distinguish route/version/resource identity from masked access or unavailable service using deployment evidence.
- `400`/`406`/`415`: compare query/payload/representation headers with the selected operation before changing versions.
- `409`/`412`: inspect resource state and concurrency contract; do not silently overwrite.
- Transport/TLS failure: distinguish DNS, proxy, trust and reachability from SAS authorization. Do not suppress certificate checks.

These are investigation branches, not guaranteed interpretations of every service's errors. Use its error schema and SAS error code/details.

## Sources and limits

Read [sources](references/sources.md) when checking provenance or refreshing this guidance. It records the consulted official contracts and access limitations. This skill has no live SAS deployment validation or blanket cross-version compatibility claim.
