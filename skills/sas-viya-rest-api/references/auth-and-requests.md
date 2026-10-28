# Authentication and request construction

## Choose an approved identity flow

Use the [SAS Logon authentication notes](https://developer.sas.com/rest-apis/SASLogon-v1/docs/getting-started/authentication.md) and deployment-matched identity documentation. The public Logon v1 contract alone does not establish what an organization's identity provider permits.

- **User-delegated work:** use the registered authorization-code flow and configured redirect requirements. Verify PKCE/client requirements against the actual deployment; do not assert a public example works unchanged with every release or client type.
- **Application work:** use client credentials only for operations supported for that application identity. Verify configured authorities/custom-group authorization. An application token is not interchangeable with a user token for services requiring a user execution identity.
- **Password grant:** do not choose it as a production shortcut. SAS's notes mark it for test/dev only and not recommended. SSO/MFA requirements cannot be bypassed by substituting this grant.
- **Refresh:** use refresh only when granted/configured for that client flow; do not assume all token responses include a refresh token.

The notes distinguish scopes from authorities and describe changed client-registration behavior. Check release-matched instructions rather than copying historical group/scope examples. Creating clients, granting group authority, and modifying access policy require separate administrative authorization.

## Build and inspect a request

1. Obtain credentials through the approved secret provider or interactive login. Keep token acquisition responses private; redact headers and sensitive URL parameters in diagnostics.
2. Verify HTTPS trust, using the deployment's approved CA bundle where necessary. Some SAS documentation examples use `curl -k` and embedded credential placeholders: do not carry that disabled verification or credential handling into the implementation.
3. Resolve the service base path from the selected OpenAPI `servers` entry and resource links. Replace example hosts; don't send credentials to a documentation/mock server. Don't put versioned documentation slugs into runtime URLs unless the contract actually requires them.
4. Send the documented Authorization scheme and media types with bounded connection/read timeouts. Encode query parameters with a client library. Distinguish SAS filter-expression escaping from URL encoding; support for filters/operators is operation-specific.
5. Parse only the documented response type. A state endpoint can return `text/plain`; do not call a JSON parser unconditionally. Inspect errors without dumping credentials, model payloads, sensitive logs or data.
6. For collections, use the operation's supported page controls and returned `next` links; constrain link origins before forwarding credentials. Detect repeated continuation links rather than looping forever. Validate uniqueness over all relevant pages before a name-based write.
7. Reconcile an ambiguous write outcome using known resource/job IDs and follow-up reads. An expired token is not justification to replay a possibly completed non-idempotent request.

## Contract discovery example (not a live execution recipe)

The [Compute v24 OpenAPI](https://developer.sas.com/api/apis/compute-v24/specifications/openapi.yml) declares a service prefix `/compute`, API version `24`, and a root GET operation. Its server hostname is an example, not an environment to contact. Read the operation's schema and returned links to choose a subsequent request. A single root response does not prove every v24 operation exists on an older deployment.

Sources consulted 2026-10-28: [Logon v1 index](https://developer.sas.com/rest-apis/SASLogon-v1/index.md), authentication notes above, and [Compute v24 usage notes](https://developer.sas.com/rest-apis/compute-v24/docs/getting-started/usage-notes.md). These procedures are original guidance, not copied sample code.
