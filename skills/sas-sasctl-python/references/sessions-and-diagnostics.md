# Sessions, authentication and diagnostics

Load for a new connection, unexpected target/identity, or connection/service failure. API names below were observed in the official **1.12.0** documentation on **2026-10-28**; confirm the installed signature and deployment authentication policy before adapting them. No connection example here was executed against SAS.

## Select one intentional authentication path

`Session(hostname, ..., token=None, client_id=None, client_secret=None)` is the documented constructor. It supports more than username/password; do not implement an OAuth grant from memory or assume an administrator's client registration is available.

| Existing deployment arrangement | Documented entry | What to establish first |
| --- | --- | --- |
| Approved access token | `Session(host, token=access_token)` | Token belongs to the intended issuer/environment and principal and has usable lifetime/scopes. Do not print/cache it for diagnosis. |
| Existing machine client using client credentials | `Session(host, client_id=client_id, client_secret=client_secret)` | Client registration, grant and authorities already exist and are approved. This is a different identity from a user login; creating clients is not connection setup. |
| Managed credential file | `Session(host, authinfo=credential_file)` | Restrict access to `.authinfo`/`.netrc`; SAS-encoded passwords in `.authinfo` are not supported by the documented path. |
| Approved interactive user/password login | `Session(host, username, password)` with password obtained securely | Deployment actually allows this flow. Never store the password in notebook/source or command history. |
| Existing SWAT REST connection | `Session(cas_connection)` | Documented reuse requires SWAT's REST connection, not its binary protocol. The CAS port is not automatically the Viya web/API endpoint. |
| Kerberos | `Session(host)` | Server configuration, client Kerberos support and an existing TGT. A hostname alone is not proof Kerberos is active. |
| Interactive authorization-code flow | `Session(host)` when other documented mechanisms do not apply | A browser/code prompt is acceptable for this interactive task. Do not let an unattended job hang waiting for it; select its approved non-interactive flow instead. |

The guide describes credential mechanisms and environment-variable fallback, not a reason to mix all of them. Inspect the relevant installed release when multiple inputs/profile/cache settings are present. Record variable **names/presence**, never their secret values. Documented variables are `SASCTL_SERVER_NAME`, `SASCTL_USER_NAME`, `SASCTL_PASSWORD`, `SASCTL_CLIENT_ID` and `SASCTL_CLIENT_SECRET`; do not invent a `SASCTL_TOKEN` variable. Environment variables are secret delivery only if the host's process/log exposure is acceptable.

### Explicit target and bounded lifetime

Non-live template for an **authorized read**, with `host` and `access_token` supplied by approved configuration/secret handling:

```python
from sasctl import Session
from sasctl.services import model_repository as mr

with Session(host, token=access_token, protocol="https", verify_ssl=True) as session:
    v = session.version_info()
    version_evidence = {
        "major": v.major, "minor": v.minor,
        "cadence": v.cadence, "release": v.release,
    }
    model = mr.get_model(model_id, refresh=True)
    if model is None:
        raise LookupError("Requested model is not visible on this target")
```

`model_id` must identify the requested model; do not copy a sample UUID. Handle the case where version discovery fails independently from the authorized resource lookup: record the sanitized discovery failure and leave version fields unknown rather than asserting Viya 3.5 or guessing a modern release. `Session.version_info()` was added in 1.8.0 and returns cached `VersionInfo`; repeated calls are not fresh upgrade evidence. Its documented `major`/`minor` distinguish 3.5 from 4.0; modern `cadence` is `stable`/`LTS`, while `release` can use `YYYY.MM` or `YYYY.R.U`. Prefer this to deprecated `platform_version()`, whose coarse 3.5/4.0 result cannot identify cadence/update.

`current_session()` gets/sets the default; calling it with connection arguments can create a new session, not merely inspect state. Task/service calls use the default target. Keep connection creation near the calls it governs, avoid interleaving different environments in a shared interpreter, and inspect `current_session()` without arguments when a notebook appears to use an old target. Do not assume global-default behavior provides per-request isolation or thread safety.

## Keep TLS verification enabled

Use the intended Viya HTTPS ingress hostname and an approved CA chain, with matching hostname and valid dates. For private CAs, the guide documents `REQUESTS_CA_BUNDLE`, `SSLCALISTLOC` and `CAS_CLIENT_SSL_CA_LIST`; its stated precedence is CAS-specific list, then `SSLCALISTLOC`, then `REQUESTS_CA_BUNDLE`. Confirm effective behavior in the installed release, especially when also using SWAT. Configure paths, not certificate contents in logs, and do not overwrite system trust stores without authorization.

An explicit `verify_ssl=True` makes intent clear but does not establish that a particular bundle was loaded. Check file readability, trust chain, proxy interception and ingress hostname when verification fails. Do not use the upstream insecure examples (`verify_ssl=False`, `SSLREQCERT=no/false`, CLI `-k`) as operational remedies.

## Diagnose the failing layer, then make the smallest supported change

| Evidence | Distinguish and inspect | Safe next action |
| --- | --- | --- |
| Import failure or unexpected keyword | Wrong interpreter/virtual environment, local module shadowing, absent optional dependency, release mismatch | Record distribution version, `sasctl.__file__`, interpreter and exact callable signature. Read matching tagged source. Do not auto-upgrade/install. |
| DNS, connection or TLS error before HTTP | Web ingress versus CAS address/port; proxy/CA/network issue | Use sanitized host, error class, trust configuration names and network policy evidence. Fix trust/routing, not authentication code. |
| Login prompt/401/token failure | Wrong grant, expired token, wrong issuer/client, cached/profile/environment precedence | Select intended auth branch and seek credential renewal through approved mechanism. Do not dump token claims/body or repeat passwords. |
| 403 | Authenticated principal lacks resource/service authority, or access policy denies operation | Check principal and requested resource/action with administrator-provided evidence. Do not create a privileged OAuth client. |
| Missing model/404/empty list | Wrong environment, no visibility, resource ID mismatch, service absent, or only the first page queried | Confirm target/ID and service route; inspect pagination/capabilities. Absence in one page is not proof nonexistence. |
| 400/415/422 | Payload/media type, variable metadata, package contents, release-specific API mismatch | Retain sanitized status and server diagnostic identifier; compare exact service contract and model content roles. Do not remove required fields merely to get acceptance. |
| Async timeout/5xx/response parsing error | Server may have accepted work; client and remote outcome can differ | Read existing job/resource state using authorized status/self links before any retry. Leave unresolved state explicitly unknown. |

For direct diagnosis, `session.get(path, timeout=(5, 30))` returns a `requests.Response`; check status/content type before parsing and summarize only safe fields. `sasctl.get(path)` is a different convenience layer returning `RestObj`/other parsed content or `None`. For server-supplied links, inspect `rel`, method and destination first; a status `GET` is not equivalent to a create/delete link. Follow only links belonging to the intended trusted deployment and allowed action.

`Session.add_logger()` and `add_stderr_logger()` can capture entire requests/responses. Leave raw tracing off by default. If strictly needed and authorized, use the documented `Session.filters` pre-logging hook with a reviewed sanitizer that removes authorization/cookies, tokens, credentials, sensitive query/body fields, model/data contents and server paths; verify the sanitizer locally with synthetic data before enabling tracing. Prefer status, error class, request/correlation ID and endpoint path over body dumps.

## Official sources

- Authentication, environment variables, TLS, logging: https://sassoftware.github.io/python-sasctl/#authentication and https://sassoftware.github.io/python-sasctl/#ssl-certificates
- Constructor, response methods, logging hooks: https://sassoftware.github.io/python-sasctl/api/sasctl.session.html
- `VersionInfo`, `current_session`, parsed REST helpers and links: https://sassoftware.github.io/python-sasctl/api/sasctl.core.html
- Version discovery addition, auth refactor and coarse API deprecation: https://github.com/sassoftware/python-sasctl/blob/v1.12.0/CHANGELOG.md

These are documentation/source observations, not live authentication, authorization, TLS or concurrency validation. Package provenance and known documentation conflicts are recorded in [../SOURCES.md](../SOURCES.md).
