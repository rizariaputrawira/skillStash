# Repository Guidelines

## Project Overview

Skillstash provides optional, standalone AI-agent guidance for SAS and SAS Viya. This is a Markdown knowledge collection, not a SAS application, SDK, installer or workflow framework. Read [README.md](README.md) for the catalog and Principles before changing scope.

## Architecture & Data Flow

The consumption path is **task → selected `skills/<name>/SKILL.md` → conditionally loaded package references → evidence-backed plan or authorized operation → verified result**. There is no shared executable runtime, application state store or dependency-injection layer.

Each entrypoint owns its core workflow; `references/` holds specialized procedures. Keep a policy with one owner and link to it rather than duplicating it: for example, decisioning's `references/assets-and-lookups.md` owns lookup activation, while `references/studio-data-grids-and-lookups.md` owns Studio preparation. Each skill must remain usable when copied alone, without sibling skills or root documents. Extend an existing owner unless a genuinely distinct invocation surface warrants a new skill.

## Key Directories

- `skills/sas-sasctl-python/`, `skills/sas-viya-python-cas/`, `skills/sas-saspy-python/`: distinct Python interfaces for Viya services, CAS actions and SAS sessions respectively; do not interchange their execution models.
- `skills/sas-viya-rest-api/`, `skills/sas-viya-decisioning-mcp/`: service-contract integration and discovered MCP decisioning tools; optional runtime/promotion branches remain conditional.
- `skills/sas-workbench-cli/`, `skills/sas-viya-agentic-ai/`: separate Workbench and Accelerator surfaces, with their own version and deployment boundaries.
- `skills/sas-trustworthy-ai-lifecycle/`: governance evidence and fraud-monitoring review, not another execution client.

## Development Commands

No repository build, application run, test runner or lint/formatter command is configured. Do not invent `npm`, Bun, Python-package or Make targets.

- `git diff --check`: check tracked changes for whitespace errors; this does not validate Markdown links or examples.
- `git diff -- README.md SOURCES.md skills/`: review catalog, provenance and skill edits before delivery.

Use focused offline checks described below. Do not add an application test framework merely to validate Markdown.

## Code Conventions & Common Patterns

- Follow [README skill naming](README.md#skill-naming): lowercase `<domain>-<capability>`, with `sas-` for SAS-specific guidance and a descriptive domain for other skills. Keep the directory and frontmatter `name` identical; use `SKILL.md` entrypoints, hyphenated reference filenames, Markdown headings and language-tagged fences.
- Keep entrypoints compact. Conditional pointers must name both the trigger and exact relative reference. Resolve links from the containing file, including fragments.
- Write ordered, actionable procedures with concrete evidence and result fields. Separate offline preparation, authorized effects and observed verification; skill selection grants no execution authority.
- Examples are compact, original and explicitly synthetic/illustrative. Never include credentials or raw customer data. Use actual target contracts rather than guessed endpoints or copied unsafe defaults.
- Error/async patterns are procedural: distinguish transport success, accepted submission, terminal completion and business correctness. Preserve resource/job identities and reconcile partial or uncertain writes before retrying. Clean up only task-owned resources when authorized.
- For version-sensitive edits, consult the owning official source and update the package-local source record with exact URLs, version/commit, date and limitations. Consulted revisions are not deployment compatibility guarantees.

## Important Files

- `README.md`: authoritative Principles, catalog, selection examples and version policy; update it when selection guidance changes.
- `SOURCES.md`: repository provenance/license index; update affected rows when source basis changes.
- `skills/<name>/SKILL.md`: actual consumer entrypoint; read it before editing its references.
- Package `SOURCES.md`, or `references/sources.md` in REST and Agentic AI: canonical detailed provenance; retain it with the package.
- `skills/sas-trustworthy-ai-lifecycle/references/lifecycle-evidence.md`: attributed **CC BY 4.0** adaptation. Preserve its attribution/license/change notices. Other original texts have no declared repository-wide license; upstream software terms do not automatically license them.

## Runtime/Tooling Preferences

Reading and editing the collection requires no prescribed language runtime or package manager. No build manifests, lockfiles, CI workflows or committed validation scripts are currently supplied. Host skill locations/discovery are host-specific; do not hard-code installation paths. SAS clients, product entitlements, credentials and live services are task-specific prerequisites, not repository setup. Do not install tools, authenticate or contact SAS simply to author guidance.

## Testing & QA

There is no configured test framework or coverage threshold. For changed guidance:

1. Check frontmatter, headings, fences and every affected relative file/fragment link.
2. Check package portability in isolation: links must resolve without siblings or root documents.
3. Parse fenced JSON where present; independently verify formulas, units and boundary cases. Do not execute SAS examples without explicit authority.
4. For meaningful workflow changes, exercise representative offline fixtures through the actual entrypoint and conditional references; inspect loaded paths and resulting artifacts, not just wording.
5. Review source pins and file-specific license boundaries. Do not copy upstream code/assets or relabel adapted material without the required review/notices.
6. Report exact checks and outcomes. Structural checks, consuming-agent behavior, native host discovery and live SAS behavior are separate claims; mark unexercised layers unverified.
