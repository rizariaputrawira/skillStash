---
name: sas-sasctl-python
description: "Use when integrating Python with SAS Viya through sasctl sessions, service calls, model registration, packaging, publishing, or scoring."
---

# Work with SAS Viya through sasctl

Use this skill as the coding agent implementing or diagnosing a requested `sasctl` workflow. It is a portable knowledge package, not permission to connect, install dependencies, register OAuth clients, or change a deployment. Keep local code preparation separate from authorized remote execution.

## 1. Establish the operation and version evidence

Record the requested endpoint environment, operation, resource identity and allowed effects before choosing a helper. Distinguish **local packaging**, **repository registration**, **destination publishing** and **scoring**: success at one stage does not establish the next.

Record these axes independently:

- Python interpreter and installed `sasctl` distribution, import location and selected callable signatures.
- Model framework/serializer versions locally and the target scoring runtime's Python/packages.
- SAS Viya **3.5**, or the modern SAS Viya platform's actual release and cadence; “Viya 4” alone is not a release/update identifier.
- Required remote service/API capabilities, model/project versions and publishing destination type.

Documentation observed on **2026-10-28** identifies itself as **sasctl 1.12.0**, built **2026-08-26**. The inspected release is tag `v1.12.0`, commit `f51cae6f71c720a31db829d79d1ef6cb9311f05a`. This is a research baseline, not a claim about the consuming environment or newest release. Consult [SOURCES.md](SOURCES.md) for exact URLs, release changes and documentation conflicts.

When working in an existing Python environment, inspect distribution metadata, import provenance and callable signatures before constructing a `Session`. This non-live example is for a trusted installed package; it was not executed while authoring:

```python
from importlib.metadata import version
import inspect
import sys
import sasctl
from sasctl import Session
from sasctl import tasks

print(sys.version.split()[0], version("sasctl"), sasctl.__file__)
for fn in (Session, tasks.register_model, tasks.publish_model):
    print(fn.__qualname__, inspect.signature(fn))
```

Do not install/upgrade to make an example fit. If the package is absent, a callable is missing, or metadata and import location disagree, use the environment manifest and version-tagged upstream source; mark installed behavior unknown. An unfamiliar keyword is a signature/version question, not a reason to pass guessed `**kwargs` or remove the requested behavior.

## 2. Choose the narrow interface and load its branch

| Requested work | Start here | Boundary |
| --- | --- | --- |
| Session, token/client authentication, TLS or wrong target | Read [references/sessions-and-diagnostics.md](references/sessions-and-diagnostics.md) | A session can authenticate immediately and affect the default target. |
| Common lifecycle operation | `sasctl.tasks` (some helpers also exported at package root) | Tasks orchestrate multiple requests and can generate files or alter project metadata. |
| Inspect/manage a specific resource | Public `sasctl.services` imports, such as `model_repository`, `model_management`, `model_publish`, `microanalytic_score` | Service wrappers are not interchangeable; inspect their exact signatures and effects. |
| Package/register/publish/score models | Read [references/model-lifecycle.md](references/model-lifecycle.md) | Select the actual model/runtime/destination path, including the Viya 3.5 branch. |
| Wrapper does not cover the requested REST operation | Verify the installed public REST helper and target API contract | `sasctl.get(...)` generally returns a parsed object; `Session.get(...)` returns a response. Do not invent endpoint paths or media types. |

Use public imports rather than copying `sasctl._services` implementation paths from generated documentation. Prefer an appropriate task only when all its side effects are in scope; use a service-level call when explicit version selection or tighter control requires it.

## 3. Implement within the permission boundary

Keep secrets out of source, notebook cells/output, URLs, debug traces and final reports. Use approved secret delivery and verified HTTPS trust; never fix authentication/TLS errors with plaintext transport, `verify_ssl=False`, CLI `--insecure`, or `SSLREQCERT=no`.

Resolve repositories/projects/models/destinations to unambiguous IDs or returned objects and record intended versions. A name lookup, default repository or `latest` selector can target something other than the requested resource. Creation, imports, score tests, publication, replacement, resource copies and deletion are remote changes, including calls used only for “validation.” Do not execute them without authorization for that target and effect. Preserve existing resources on failure; never auto-delete or republish to recover an uncertain outcome.

## 4. Prove only the outcome actually observed

For authorized execution, capture minimal sanitized evidence at the layer requested:

- **Packaged:** local artifact names/content roles and reviewed score contract; no remote outcome implied.
- **Registered:** fresh server read of returned model ID, intended project/version and required model contents.
- **Published:** completed publishing state/log and destination identity, then a fresh destination-specific read. HTTP acceptance or a returned local object is insufficient.
- **Scored:** actual remote execution identity, input/output schema, sanitized result and comparison with an approved known local prediction/tolerance. Published is not scored.

Use explicit fresh-read options where supported; passing a complete `RestObj` to a get helper can simply return that object. Respect pending/failed/unknown states. After a timeout or broken response, inspect the existing job/resource before resubmission; client failure does not prove server rollback. If execution is unavailable or unauthorized, finish code/package analysis and label the remote result **not run**, naming the missing prerequisite rather than simulating success.

## Report contract

Return: **requested operation and effects performed**; **Python/sasctl/model-runtime evidence**; **Viya release/cadence and service/API facts or unknowns**; **chosen callable and why**; **resource/version/destination identity**; **observed local versus remote result**; **remaining prerequisite or failure with sanitized diagnostic evidence**; and **exact official source URLs/release applicability**. Do not include tokens, credentials, raw training records, full request/response dumps or a broad compatibility claim.
