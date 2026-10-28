# Sources, version and license boundaries

## Observed release

Research performed **2026-10-28**. Target baseline: SAS Agentic AI Accelerator **2.4.1**, release tag `2.4.1`; GitHub release metadata reports publication **2026-10-27**. Tag and release identity observed at:

- [Release 2.4.1](https://github.com/sassoftware/sas-agentic-ai-accelerator/releases/tag/2.4.1)
- [Release metadata](https://api.github.com/repos/sassoftware/sas-agentic-ai-accelerator/releases/tags/2.4.1)
- [Tagged source tree](https://github.com/sassoftware/sas-agentic-ai-accelerator/tree/2.4.1)
- [Tagged changelog](https://raw.githubusercontent.com/sassoftware/sas-agentic-ai-accelerator/2.4.1/CHANGELOG.md)

The release notes describe a smaller Prompt Builder page, removal of `'unsafe-eval'` reliance for that page, and an optional corresponding CSP tightening. The release's **Administration Guide** at the moving hosted docs says SAS Viya **2026.09+**, Python **3.11+**, SAS administrator and Kubernetes/registry capabilities are prerequisites. The 2.4.1 tagged `Model-Definition-Builder/cli/pyproject.toml` independently sets `requires-python = ">=3.11"` and project version dynamically from `mdb.__version__`. The hosted Docusaurus pages identify themselves as `current`, not as immutable 2.4.1 pages. Recheck tagged source/changelog and the target installation before use; this record does not certify 2.4.1 on any environment.

First-party source pages consulted:

| Source | Observed use |
| --- | --- |
| [Administration introduction](https://sassoftware.github.io/sas-agentic-ai-accelerator/Administration-Guide/Introduction) | Platform/Python/admin prerequisites; hosted page is mutable `current` |
| [Model Definition Builder guide](https://sassoftware.github.io/sas-agentic-ai-accelerator/Administration-Guide/Model-Definition-Builder) | Definition workflow, commands, Python dependency, endpoint modes, separate definitions repository |
| [Managing Credentials](https://sassoftware.github.io/sas-agentic-ai-accelerator/Administration-Guide/Managing-Credentials) | Credential-domain identity model and credentials workflow |
| [Deploying Builder UIs](https://sassoftware.github.io/sas-agentic-ai-accelerator/Administration-Guide/Setup-Additional-UIs) | Transfer-package/import boundaries and report options |
| [Deployment of Decisions](https://sassoftware.github.io/sas-agentic-ai-accelerator/Administration-Guide/Deployment-of-Decisions) | Prompt/decision runtime, TLS and MAS/SCR differences; observed specific deployment measurements are not generalized |
| [Prompt Builder user guide](https://sassoftware.github.io/sas-agentic-ai-accelerator/User-Guide/Prompt-Builder) | Prompt experiment/manifest/optimization workflow |
| [Tagged CLI metadata](https://raw.githubusercontent.com/sassoftware/sas-agentic-ai-accelerator/2.4.1/Model-Definition-Builder/cli/pyproject.toml) | Package name `sas-mdb`, Python minimum, base and optional dependency declarations |

## Provenance of this Skillstash package

The Skillstash entrypoint and references are independently written procedural guidance and original compact examples informed by the linked sources. No accelerator Python, SAS, JavaScript, HTML, report, model definition, static image, third-party package, or accelerator license file is copied into this skill folder. The 2.4.1 release is a research/source reference, not a vendored dependency or claim of SAS endorsement. The skill's use of accelerator names is descriptive.

## Upstream licensing observed

The accelerator's [tagged README](https://raw.githubusercontent.com/sassoftware/sas-agentic-ai-accelerator/2.4.1/README.md) states that the project is under **Apache License 2.0 except for `/static`**. It says `/static` elements are SAS-owned and not released under an open-source license; commercial SAS Viya licenses are separate and required to use the capabilities with SAS software. The [tagged LICENSE](https://raw.githubusercontent.com/sassoftware/sas-agentic-ai-accelerator/2.4.1/LICENSE) contains the Apache-2.0 text. The release tree's `static/` directory includes `sas.png`; no such asset is copied here.

The same README lists Python, pandas (BSD-3-Clause), HuggingFace Hub (Apache-2.0), `sasctl` (Apache-2.0), `kubectl` (Apache-2.0), and `js-tiktoken` (MIT for the Visual Analytics usage report), and says it uses the [SAS Portal Framework for SAS Viya](https://github.com/sassoftware/sas-portal-framework-for-sas-viya). The Portal Framework README itself describes `/static` as excluded from its Apache grant and identifies Bootstrap (MIT), Zero-MD (ICS), and SAS SDKs/CDN components. These are not bundled in this Skillstash package. The accelerator README says model deployment recipes do not include the models' own licenses/dependencies: independently inspect each model, tokenizer, weights, provider terms and runtime package before use. This table is not a full SBOM; transitive dependencies, generated outputs, license notices in a deployment image and all model terms were not audited.

`mdb`'s tagged project metadata declares its own `sas-mdb` CLI as Apache-2.0, but that is not a license grant for SAS Viya, model weights, all Python requirements, generated definitions, accelerator assets, or this independently authored skill. Do not redistribute the static-logo asset or assume the accelerator's exception-free Apache grant applies to excluded files.

## License review result and limits

- **Observed local material:** original prose, source URLs and original short instructional examples only; no code/assets copied from the accelerator release. No accelerator `LICENSE`, `NOTICE`, image, runtime dependency, or static content included.
- **No repository-wide Skillstash license is declared.** Links and provenance notes do not select or grant a license for original Skillstash text. Do not infer one from upstream Apache labels.
- **Upstream observations are version-scoped** to accelerator 2.4.1, `sasctl` 1.12.0, SWAT 1.18.1 as applicable. Verify exact versions and file-level exclusions before copying anything in future changes. SWAT's Python code is Apache-2.0; the same tagged license document states bundled TK libraries/C extensions are under separate SAS License Agreement terms. The SWAT skill bundles neither.
- This is source-only evidence, not legal clearance or a full dependency/model/image audit. If content is later copied, packaged, deployed, or distributed, inspect exact source files, applicable license/NOTICE, transitive dependencies, model terms, recipient activity, SAS entitlements and required notices first.
