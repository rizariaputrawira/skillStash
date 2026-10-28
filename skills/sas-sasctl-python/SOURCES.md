# Sources and research limits

## Identity and provenance

This folder is independently authored guidance for a coding agent using the SAS-maintained `sasctl` Python package. It replaces the previous draft in this folder. The workflow, decision tables and compact examples are original synthesis of first-party documentation and source observations, not a copied upstream skill or vendored implementation. No Kitshift, Leanflow, sibling skill or repository-root file is required to use it. SAS documentation is reference authority for API behavior; this folder does not represent SAS endorsement or grant access to any SAS deployment.

All observations below were made **2026-10-28**. The generated documentation identifies itself as **sasctl 1.12.0**, last updated **2026-08-26**. The inspected GitHub release is **v1.12.0**, with release commit **f51cae6f71c720a31db829d79d1ef6cb9311f05a**. These are observed versions, not a promise that the site is version-pinned or that 1.12.0 is the newest available distribution. Tagged source URLs are preferred for reproducible implementation facts; the generated site and repository `master` can move.

Upstream `v1.12.0/LICENSE` is **Apache License 2.0**, and source headers identify SAS Institute Inc. This is an observation about the upstream package, not a newly assigned license for this independently authored folder, third-party model assets or SAS documentation. No upstream implementation files, binaries, notebooks or license text are bundled here. Preserve applicable notices and review redistribution terms separately if later adding copied material.

## Exact first-party sources consulted

| Source | URL | Observed use |
| --- | --- | --- |
| Project and README | https://github.com/sassoftware/python-sasctl | SAS-maintained package identity; task/service/REST layering; examples and prerequisites. README badges/install advice were not accepted as compatibility evidence. |
| Documentation entry/user guide | https://sassoftware.github.io/python-sasctl/ | 1.12.0/build date; authentication choices, environment variables, TLS, logging, HATEOAS. |
| Release page | https://github.com/sassoftware/python-sasctl/releases/tag/v1.12.0 | Release identity, commit and 1.12.0 changes. |
| Release changelog | https://github.com/sassoftware/python-sasctl/blob/v1.12.0/CHANGELOG.md | Historical behavior changes, supported-path cautions, deprecations. Content retrieved at https://raw.githubusercontent.com/sassoftware/python-sasctl/v1.12.0/CHANGELOG.md. |
| Moving changelog, compared during research | https://github.com/sassoftware/python-sasctl/blob/master/CHANGELOG.md | Observed heading v1.12.0 (2026-08-26); not used to claim a future/latest version. |
| Session API | https://sassoftware.github.io/python-sasctl/api/sasctl.session.html | Constructor/token/client auth, logging hooks, HTTP response methods and version discovery. |
| Core API | https://sassoftware.github.io/python-sasctl/api/sasctl.core.html | `VersionInfo`, cached discovery, default-session API, parsed REST helpers and links. |
| Current-session page | https://sassoftware.github.io/python-sasctl/api/sasctl.current_session.html | Page had a heading without useful contract details; substantive contract taken from core API instead. |
| Tasks API | https://sassoftware.github.io/python-sasctl/api/sasctl.tasks.html | Register/publish/score/upload signatures and lifecycle distinctions. |
| Task implementation at release tag | https://github.com/sassoftware/python-sasctl/blob/v1.12.0/src/sasctl/tasks.py | Registered/code-supplied publish routing, monitoring, MAS delete-and-republish behavior, lack of kwargs forwarding on registered branch. Content retrieved at https://raw.githubusercontent.com/sassoftware/python-sasctl/v1.12.0/src/sasctl/tasks.py. |
| Package exports | https://raw.githubusercontent.com/sassoftware/python-sasctl/v1.12.0/src/sasctl/__init__.py | Declares 1.12.0; verifies root exports versus task-module helpers. |
| Packaging metadata | https://raw.githubusercontent.com/sassoftware/python-sasctl/v1.12.0/setup.py | Dependency metadata and contradictory Python floor/classifiers; not a validated runtime matrix. |
| Import-model API | https://sassoftware.github.io/python-sasctl/api/pzmm/import_model.html | Score generation prerequisites, tuple return, overwrite effects, target-index conflict and Viya 3.5 branch. |
| ZIP API | https://sassoftware.github.io/python-sasctl/api/pzmm/zip_model.html | Local/in-memory packaging and explicit 3.5 versus modern flag. |
| JSON/dependencies API | https://sassoftware.github.io/python-sasctl/api/pzmm/write_json_files.html | Exact metadata-builder names, file roles, development-environment dependency warning and text requirements option. |
| Serialization API | https://sassoftware.github.io/python-sasctl/api/pzmm/pickle_model.html | `PickleModel.pickle_trained_model` and local/in-memory output behavior. |
| Score-code API | https://sassoftware.github.io/python-sasctl/api/pzmm/write_score_code.html | `ScoreCode.write_score_code`, input/output/preprocessing options and file generation. |
| Model Repository API | https://sassoftware.github.io/python-sasctl/api/services/model_repository.html | Fresh reads, contents/details, model versions, 3.5 DS2 wrapping and asynchronous resource copies. |
| Model Publish API | https://sassoftware.github.io/python-sasctl/api/services/model_publish.html | Destination lookup/configuration; `publish_model` marked deprecated since 1.11.5. |
| Model Management API | https://sassoftware.github.io/python-sasctl/api/services/model_management.html | Explicit model-version publication and distinct force/reload arguments. |
| MAS API | https://sassoftware.github.io/python-sasctl/api/services/microanalytic_score.html | Module/step discovery, explicit execution and IDs that need not be UUIDs. |
| Upstream license | https://github.com/sassoftware/python-sasctl/blob/v1.12.0/LICENSE | Apache-2.0 license observation; content retrieved at https://raw.githubusercontent.com/sassoftware/python-sasctl/v1.12.0/LICENSE. |

## Release facts that change implementation choices

- **1.12.0 / 2026-08-26:** binary-classifier registration returns both classes; scoring Gateway default becomes true; `input_mapping` added. Code written for earlier defaults/schema needs review.
- **1.11.6 / 2025-11-18:** optional `create_requirements_txt` added to dependency generation.
- **1.11.5 / 2025-06-27:** model-version endpoint methods and configurable CAS score-test timeout added. Do not assume older installations expose these.
- **1.11.3 / 2025-04-29**, **1.11.4 / 2025-05-02:** local-directory upload introduced, then ASTORE intake improved.
- **1.11.0 / 2024-10-29:** score definition/execution and optional Gateway path added; Gateway requires configured environment. Preprocessing-function support added.
- **1.10.7 / 2024-10-02:** changelog states package no longer available through Anaconda, contrary to README installation prose.
- **1.10.4 / 2024-07-08:** changelog says Python 3.6/3.7 support dropped. Tagged setup metadata still says `python_requires=">=3.6"` and lists those classifiers; a stale badge or permissive installer bound is not a support guarantee.
- **1.9.0 / 2023-04-04:** pzmm refactor/in-memory generation; older camelCase/archive examples are not automatically current API recipes.
- **1.8.0 / 2022-12-19:** `Session.version_info()` introduced and `platform_version()` deprecated; explicit Viya 3.5 versus modern differences exist rather than one undifferentiated “3.5+” workflow.

## Conflicts and unresolved applicability

The 1.12.0 guide still shows `input=` in examples while the tasks reference deprecates it for `X`. The model import API renders `target_index=None` in its signature but describes default 1 in prose. The repository `create_model` page documents a `project_version` parameter not visible in its rendered signature. The tasks docs describe publish kwargs generically; the inspected tagged implementation only forwards them to the code-supplied branch. Resolve each relevant discrepancy with the installed callable and matching source, not with a silent fallback or an incompatible keyword.

No SAS endpoint was contacted, no package installed or imported, no Python example executed, no OAuth credential obtained and no model uploaded/published/scored during this authoring work. Installed Python/sasctl/framework versions, server release/cadence, service versions, authentication policy, privileges, TLS configuration and destination/runtime capabilities remain unknown. Source review establishes documented names and source-level behavior only; it does not establish broad compatibility, host discovery behavior, successful live authentication or model accuracy. A consuming workflow must acquire its own authorized target-specific evidence.
