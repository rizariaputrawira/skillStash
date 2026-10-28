# Connection selection and trust

Use the [v5.110.0 configuration reference](https://github.com/sassoftware/saspy/blob/v5.110.0/saspy/doc/source/configuration.rst) as the research baseline, then match the installed version and target policy. Constructing `SASsession` can authenticate and start a server session; it is not passive version discovery.

## Select by actual execution target

| Target | Access method | Establish before connection |
| --- | --- | --- |
| Viya Compute | HTTP over approved HTTPS | Viya origin/proxy routing, Compute context, supported OAuth route and trusted CA |
| SAS Workspace Server, including SAS 9.4/Grid | IOM using Java | Workspace host/port, authentication, Java/JAR compatibility and server capabilities |
| SAS on the same Linux host | STDIO | Actual SAS executable, launch options, encoding and process ownership |
| Remote Linux SAS | SSH (STDIO over SSH) | Approved host/key/host-key verification, SAS path and transport/data-transfer requirements |
| Existing Windows COM integration | COM | Existing supported site configuration; upstream warns this method is not fully supported and recommends IOM instead |

An Enterprise Guide/Workspace connection is not automatically a Viya Compute connection. HTTP does not connect to SAS 9.4. Do not infer an access method from the Python host OS alone or install IOM Java dependencies for an HTTP-only task.

## Configuration ownership

Use a trusted, site-specific `sascfg_personal.py`, not edits to the installed example `sascfg.py` that upgrades can replace. Config files are Python code: inspect provenance before importing them. Avoid implicit discovery from the current directory or package defaults when multiple environments exist. Select an approved `cfgfile` and `cfgname`, and inspect the installed configuration contract before use. Upstream documents import caching and different behavior for explicitly supplied config files; changing working directory does not prove a different profile was loaded.

Check `SAS_config_options`/`lock_down` and the effective profile: runtime kwargs can be ignored under configuration restrictions. Do not bypass a locked profile or claim a keyword took effect merely because the constructor accepted it. Preserve unrelated profiles and keep secrets out of version control.

## Viya Compute / HTTP

1. Confirm an approved HTTPS origin and explicit Compute context. Omitted context can cause interactive discovery/prompting; do not let an unattended script pick an arbitrary available context. Context affects runtime identity, autoexecs, libraries and filesystem access.
2. Choose **one** supported authentication route: approved authorization-code flow (PKCE where configured/supported), an externally obtained Viya access token (`authtoken`, optionally `refreshtoken`), or site-approved credentials through protected delivery. Do not mix auth routes, assume a default SASPy OAuth client exists, or reuse a foreign provider JWT as a Viya access token. The tagged docs flag changes/deprecation for the JWT route using the SASPy client. OAuth registration/authority changes are separate administrator work.
3. Set **`verify=True` explicitly in the effective HTTP profile** and use `cafile` for the approved client-side CA bundle when needed. At v5.110.0, `verify=None` can fall back to an unverified HTTPS connection after a certificate error; HTTPS alone is insufficient. Never fix trust errors by disabling verification or using HTTP. Inspect the effective profile rather than relying on a possibly ignored runtime override.
4. Keep auth codes, access/refresh tokens, passwords, proxy secrets and authorization URLs containing private authentication parameters out of chat, logs and notebook history. Use the organization's approved secret mechanism; protected authinfo files are not encrypted vaults. Do not dump the profile/session object to inspect it.
5. After authorized creation, use the identity probe in [submission and lifecycle](submission-and-lifecycle.md), then verify the intended context's access with a bounded non-sensitive operation. Authentication is not proof of library access or licensed procedure availability.

## SAS 9.4 / IOM, SSH and STDIO

For IOM, match the installed SASPy release's Java/classpath instructions to the actual Workspace Server and authentication scheme. Java client binaries have separate licensing; see [SOURCES.md](../SOURCES.md). Do not assume any Java version/JAR combination works or that a SAS server installation is provided by `pip install saspy`.

For SSH, verify the approved remote host and host key without suppressing SSH checks. SAS paths and librefs resolve on the remote SAS side. Transfer methods can require separate port/network behavior; diagnose from the selected method rather than opening broad firewall access. For STDIO, verify the local executable is the intended SAS installation and inspect launch options/autoexec before starting it.

Finish diagnosis with the failing layer: package/config loading, Java/process/SSH transport, TLS, authentication, context/session creation, library access, or SAS program execution. Preserve existing resources; do not switch to another server or weaker authentication to turn a failed check green.
