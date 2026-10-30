---
name: sas-viya-decisioning-mcp
description: "Use when inspecting, authoring, testing or publishing SAS Viya decisioning assets through a configured SAS MCP server, including supported parts of a Fraud Decisioning implementation."
---

# MCP-aware SAS decisioning

Use this skill as the coding agent implementing or reviewing an authorized decisioning task. MCP provides callable tools; this skill supplies the procedure for selecting assets/revisions, controlling effects and proving outcomes. It does not install a server, configure a host, grant Viya access, or require another Skillstash skill.

The research baseline is **SAS MCP Server v1.19.0**, released **2026-10-23**, commit **`e500fd4d6dac2bd46b8ae67f52934bbe6f3e381c`**. Read [SOURCES.md](SOURCES.md) for version changes, exact sources and licensing. Tool names below are upstream names, not guaranteed host-visible names or registered tools in the current session.

## 1. Bind the product and task

Record the exact SAS product and installed release: **SAS Fraud Decisioning is not automatically interchangeable with SAS Intelligent Decisioning**. Establish which required decision/rule/model/lookup operations the target actually supports, the Viya platform release/cadence/updates, MCP server version/image or commit, transport, enabled tool surface, Viya identity and environment. A package version, tier number or public README does not establish deployed capabilities or product entitlement.

Identify the requested stage, acceptance cases, target folder, asset IDs/revisions, downstream consumers and permitted effects. Keep inspection, compilation/testing, authoring, revision locking, lookup activation, publication and scoring distinct. A request to review fraud logic does not authorize creating a decision, running customer transactions or publishing into production. Unknown product support blocks a compatibility claim or dependent remote action, not offline specification/code preparation.

For Fraud Decisioning, obtain classification/schema, organization/project, profile state/update order and rule-phase-to-runtime evidence from the supplied strategy specification before mapping it to tools. Do not treat Intelligent Decisioning rule sets/lookups or MAS publication as automatic equivalents of Fraud Decisioning rules/lists or deployment. If the task is strategy design rather than tool execution, prepare the product-specific specification offline; MCP availability is not a prerequisite for that work.

## 2. Discover before choosing a tool

Read [discovery and access](references/discovery-and-access.md) before live discovery or any MCP call. Use the host's actual available MCP discovery interface to inspect the configured server's tools, descriptions, input schemas and annotations; verify endpoint/identity from approved non-secret evidence. Do not invent a tool route, guess a schema from this skill, or claim a repository link has connected the server.

Match the task to the narrow available tool. Prefer a suitable configured MCP tool when all its effects are authorized, rather than rebuilding its REST orchestration. If it is absent, distinguish version, tier/read-only filtering, host caching/renaming and actual service permissions. Do not bypass deliberate filtering/denial by switching transport, direct REST or arbitrary SAS execution. A separately authorized REST client remains appropriate for uncovered operations or a task explicitly requesting one; verify its exact service contract.

## 3. Inspect and prepare the change

For rule sets, flows, revisions, advanced assets or lookup data, read [asset authoring and lookups](references/assets-and-lookups.md). Read the exact current asset/revision, folder, signature, rules/step order, lookup versions and consumers before proposing a change. Resolve display names to unambiguous IDs. Summaries, capped lists or a single page are not a full dependency/content audit.

Translate the approved business specification into typed inputs/outputs and explicit missing/default behavior. Use approved synthetic edge cases for thresholds, overlapping conditions, priority/order and data-type conversions. Keep account/card/transaction identifiers as identifiers; do not change their representation or expose real fraud/customer data to model context just to test a rule.

Review the proposed delta, exact revision dependencies, automatic helper effects, output-variable additions, activation/publication destinations and consumer impact. Verify concurrency protection where provided. A tool's convenience behavior cannot silently widen the authorized change.

## 4. Apply and verify only authorized stages

For supported authoring, re-read the stored asset after the write and inspect `problems`, compilation warnings, folder result and `steps_report` where returned. A saved object or valid expression is not proof that a full decision compiles, publishes or implements the business policy. Resolve partial/uncertain writes before retrying.

For locking, publication, MAS signature inspection or scoring, read [publication and verification](references/publication-and-verification.md). Preserve the locked revision ID separately from the new working revision; record actual runtime module/step IDs and lookup activation state. Do not infer publication from an accepted request or analytical success from an MCP success envelope.

For failures/timeouts, preserve sanitized job/resource evidence and inspect current target state before resubmitting. Do not auto-delete/recreate assets, reactivate lookups, republish, reset sessions or broaden identity/permissions as error recovery. Follow the assignment's approval policy for destructive/production effects.

## Result contract

Return: actual product/Viya/MCP version and capability evidence; available tool names/schemas used; identity/environment without credentials; requested stage and effects performed; folder/asset/dependency/locked revision IDs; lookup version/activation and downstream impact; publication record/module/step identity; observed business test results and diagnostics; remaining unknowns and exact source URLs. Distinguish prepared guidance, source-checked contracts and live observations. If no authorized server exists, finish offline preparation and state that MCP discovery and Viya behavior were not exercised.
