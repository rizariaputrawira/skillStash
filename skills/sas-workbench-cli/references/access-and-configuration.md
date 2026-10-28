# Access, configuration and automation boundary

Baseline: [pinned README](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/README.md) and [token cache implementation](https://github.com/sassoftware/wbctl/blob/fd05d61b9ebe1750c0e7c853a7a2e06aab60dfc3/internal/auth/token_store.go). Verify installed help before using a profile or flag; profile support belongs to this source baseline, not every historical binary.

## Bind the effective target

`wbctl` documents `$HOME/.wbctl/config.json`, with `WBCONFIG` selecting another config file. `--profile` selects a profile; top-level options serve as defaults for omitted profile fields. If profiles exist and no profile is specified, the documented selection is `Default`. Inspect the effective endpoint, organization and profile together rather than assuming a profile name isolates tenants or environments.

The following original example is **synthetic non-secret configuration**, not a deployable endpoint. Replace the `.invalid` host and organization using approved site information. Its JSON syntax is valid; some upstream README multi-profile examples have trailing/missing commas and must not be copied literally.

```json
{
  "fraud-dev": {
    "auth-mode": "device",
    "endpoint": "https://workbench.example.invalid",
    "organization": "example-organization"
  }
}
```

Use explicit profile selection on every operation and verify effective defaults/overrides against the installed build. Keep TLS validation enabled and use the deployment's approved trust configuration. Do not bypass trust checks, widen authorities, or redirect to another endpoint to make a failed request succeed.

## Authentication and sensitive output

The documented user-facing modes are **device** and **PKCE**, with device the default. Login caches tokens; ordinary resource commands can obtain authentication too. A read-only server request may therefore prompt for browser interaction or write the local token cache. `whoami` can establish identity but is still an authenticated operation, not an offline check.

The inspected token store holds access/ID/refresh tokens in the user's token cache; it is not the profile config. `WBCONFIG` alone does not establish token-cache isolation. Verify endpoint/organization and actual identity before every environment-changing workflow. Do not read or print cache contents to prove login. Use non-secret metadata or an authorized identity request. Logout changes local authentication state and must not disrupt another workflow without authorization.

Keep device codes, OAuth callback URLs/codes, cached tokens, cookies and credentials out of chat, source, shell history and artifacts. Do not enable verbose traces as a default. The pinned submission service logs request bodies at info level, and its describe path prints environment entries; `--environment`, JSON output, dry-run output, describe and logs can expose sensitive values. Use only reviewed non-secret environment values unless the site provides an approved secret-delivery mechanism. This skill does not invent a secret-reference field or assert CLI-wide redaction.

## Unattended/coordinator execution

Do not assume device/PKCE flows provide unattended service-account login. Establish the supported authentication lifecycle, token renewal, least-privilege identity, credential delivery and expiry handling for the actual deployment before automating. Do not turn an interactive user's cached session into an assumed production robot identity or script browser passwords.

For missing/expired credentials or denied access, stop the affected execution path and use the approved sign-in/authorization process. Permission errors are not a reason to broaden roles or launch the job under another identity. Local JSON/shell preparation can continue without login; never run a server-contacting dry-run just to claim offline validation.
