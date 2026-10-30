# Prepare, configure, test, and operate BOSS orchestration

Load when the task is to build or change a SAS Business Orchestration Services (BOSS) project: receiving transactions, validating and mapping messages, obtaining enrichment, invoking detection, or returning/routing results. The consuming role is an integration designer working with a deployment administrator and the owners of each connected system.

This is a practical procedure, not an instruction to execute it now. Authoring project definitions and synthetic acceptance cases does not require a live SAS environment. Saving a project in SAS, deploying/redeploying it, sending requests, reading protected logs, consuming broker messages, or changing secrets requires the corresponding assignment and target authorization. This reference performs none of those effects.

## Source basis and applicability

Technical source actually consulted: SAS Institute Inc., *SAS Business Orchestration Services: User’s Guide*, **2026.03**, cover date **May 5, 2026**, copyright 2026, identifier `v_032-P3:eopug`. Local source locator: `.sources/archive/partially-reviewed/2026.03/SAS BOSS - User Guide.pdf`. SHA-256: `115a7500a113769f6deb47ed1a068365e8e574edfab1625b717b5dcd9e3937b4`. The locator is provenance, not a runtime dependency of this recipe.

Printed pages consulted for this procedure:

- Cover/copyright/contents; pp. **7–8, 11–22**: project hierarchy, action versions, message content/metadata, configuration structure.
- pp. **38–41, 49–52, 69–74, 83–88, 102–106, 133, 136–139**: generated/HTTP input, branches/cache, HTTP enrichment, mapping/OnDemand Decision Engine, conditional/error/validation actions, response output, batching.
- pp. **185–194, 199–207, 226–232, 242–244**: SQL parameterization context, Detection Engine integration, variable-set validation, workload administration, mappings and filtering.
- pp. **394–399, 416–418, 419–429, 431–434, 581, 585–586, 591–611**: security, errors, logging, deployment prerequisites, UI privileges and project/deployment procedures.

Official discovery: [SAS Fraud Decisioning support](https://support.sas.com/en/software/fraud-decisioning-support.html) links the [BOSS Help Center](https://documentation.sas.com/?cdcId=eopcdc&cdcVersion=v_038&docsetId=eopug&docsetTarget=titlepage.htm). That public link is a different documentation collection version from the local manual; its shell is not evidence that its technical content was consulted. In an installed UI, **? → Help Center** is the documented help route (p. 586). Select documentation matching the target cadence before execution.

The procedures below synthesize documented configuration facts. The synthetic field names, fixture enrichment, acceptance table, evidence artifacts, and approval gates are original safeguards/example design, not SAS defaults or a SAS fraud-message specification. No source text, sample project, screenshot, or production configuration is reproduced.

## 1. Prepare the project contract

1. Record target BOSS cadence, deployment method (UI-managed or administrator-managed worker container), environment, project owner, connected-system owners, and permitted effects. **Finish:** these are named, or the affected operational step is marked blocked. An unknown release does not block writing the design.
2. Obtain actual inbound/outbound schemas and sample synthetic messages, detection request/response contract, correlation key, enrichment contract, endpoint/broker details, security requirements, and owner-approved response/error semantics. Identify whether the target is **SAS Detection Engine** or **SAS OnDemand Decision Engine**; they use different integration surfaces here.
3. Define one stage table: stage/action, input fields/types, resulting fields/types, metadata, success condition, failure route, destination, and expected evidence. Separate transformation/enrichment success from a detection result and from any downstream action.
4. Identify the responsible SAS UI privileges (pp. 585–586): **Business Orchestration Users** can view; **Designers** can create/edit/archive/delete projects; **Deployers** can deploy projects and delete deployments; **Administrators** have full BOSS access. A SAS administrator assigns custom-group membership through SAS Environment Manager. These privileges do not replace business authorization for connected-system effects.
5. Pin the tested cadence and relevant action versions. The manual says omitted action versions use version 1, but action behavior can change between cadence releases even for an existing action version (p. 12). Pre-production and production cadence must match for promotion. Review experimental actions explicitly rather than treating their presence as production readiness.

**Output:** target/authority record, interface contracts, stage table, and a list of missing prerequisites with the specific stage each blocks.

When enrichment freshness, timeout uncertainty, queued delivery, substitute/late results, or downstream feedback matters, read [orchestration-and-feedback.md](orchestration-and-feedback.md). That reference owns the state/effect ledger and reconciliation safeguards; apply it alongside this configuration recipe rather than replacing it with transport-status checks.

For additional broker/file/cache/SQL/conversion/batch patterns, read [action selection](boss-actions.md). For mapping syntax, functions and interpolation, read [expression language](boss-expression-language.md). For editor/schema validation and its limits, read [schema contracts](boss-schema-contract.md). These are conditional functional references, not a claim that every source property or example has completed review.

## 2. Design the actual configuration surface

BOSS uses a project definition written in its YAML-based expression-language configuration, not a presumed orchestration REST API or a visual flow-builder contract (pp. 11–22, 594–596).

| Layer/action | Configure | Completion evidence |
| --- | --- | --- |
| Project | Required `id`, `version`, `workloads`; optional `label`, `details`. Project `version` is not the product cadence. | Identifiable project definition and revision. |
| Workload | Required `flows`; optional `configurations.http` and `configurations.logger`. A workload is independently scalable; its flows share a runtime. | Intentional isolation/scaling boundary. |
| Flow | Required `inputs`, `processors`, `outputs`; one or more inputs/outputs and zero or more processors. | Ordered stage table matches configuration. |
| HTTP input | `httpServer.path`, `allowedVerbs`, `timeout`, `syncResponse.status`, `syncResponse.headers`, optional `metadataHeaders.includePrefixes`. | Exact route/method and response contract. |
| Mapping | `map.mapping`; `this` is current input, `root` is constructed output. Begin with `root = this` when preserving existing fields, then assign additions. | Before/after message comparison, including intentionally removed fields. |
| Branch enrichment | `branch.requestMap`, `processors`, `resultMap`. Create the enrichment request separately and map the branch result into the original message. | Original transaction identity survives; enrichment has its own field. |
| Conditional work | `switch.cases`, each with optional `check` and `processors`. Only the first matching case executes; an omitted `check` matches unconditionally. | Positive, negative, and default cases exercised in the planned order. |
| Synchronous result | `response: {}` sends the final payload to the input origin; for HTTP, use the input's response configuration. | Caller receives the specified final message and status. |

Do not confuse the output `httpServer` action with the `response` output: the former exposes an output endpoint clients can call for flow results (p. 128); the latter returns the final message to its input origin (p. 133).

Avoid overlapping HTTP action paths. A trailing slash matches subordinate path segments and registration order is unpredictable (pp. 205–206). For UI deployments, keep the workload HTTP server port at **4195**; the manual says it must not be changed for this deployment method (p. 205). `synchronousOnly` is deprecated and ignored in this edition, not a way to enforce synchronous processing (p. 19).

### Validate before enrichment or detection

- For an ordinary JSON contract, use `validateWithJSONSchema.source` with the selected `schema` text or `schemaPath` source. Validation does not modify the payload; a validation error requires subsequent error handling (pp. 104–105).
- For a SAS Detection Architecture message contract, use `validateWithMessageSchema.schemaPath` and, if needed, `requireVariableSets` (pp. 105–106, 192–194). Use the actual exported schema and actual variable-set names. A required child can still be absent without an error when its optional parent is absent; test parent-absent and parent-present cases separately. An invalid required-variable-set name is an error.
- Revalidate mapped/enriched detection input when its contract differs from the inbound contract. A valid inbound message is not automatically valid detection input.

**Finish:** the configuration has a deliberate rejection/error route and does not proceed to detection after failed validation.

### Configure external enrichment without losing the original transaction

The `http` **processor** sends the current message as the request body and **replaces** that message with the response body (pp. 69–74). Wrap it in a `branch` when the transaction must be retained:

1. Set `requestMap` to construct only the enrichment provider's required request fields.
2. Place an `http` processor in `branch.processors`: set the provider's actual `url`, `verb`, `headers`, and `timeout`; set supported `tls` and, if required by the provider, `oauth2` properties.
3. Map the returned provider fields into a named enrichment object using `resultMap`. An empty result mapping leaves the origin unchanged; it is not automatic enrichment.
4. Validate response shape and record the provider's actual provenance/freshness fields where its contract supplies them. Decide missing/error handling with its owner, using the state-review reference for freshness and uncertainty.
5. Compare original identity/amount fields and enrichment values against the expected merged message before enabling detection.

For HTTP processor OAuth, documented fields are `enabled`, `clientKey`, `clientSecret`, `scopes`, and `tokenUrl`; token acquisition/renewal uses the client-credentials grant. Do not infer other grants or SAS service authentication from this generic processor support.

HTTP retry controls are real configurable properties: `maxRetries`, `retryInitialWait`, `retryMaxWait`, `retryStatusCodes`, `failureStatusCodes`, `successStatusCodes`. The documented wait grows exponentially to the configured maximum. Explicit failure codes are non-retryable; 2xx is normally successful unless configured otherwise (pp. 69–74). Set a reviewed policy for each endpoint: the source's example/default values are **not** a universal retry policy or an SLA. Account for the caller timeout, retries, batch waiting, enrichment, and detection together; a request timeout alone does not establish the eventual state of a connected system.

### Select the detection branch only after its contract is available

- **SAS Detection Engine:** the manual's pp. 191–192 procedure uses an `http` processor, POST to the engine's **`/decision/execute`** endpoint, with appropriate `Content-Type` and `Accept` headers (`application/json` or `application/ion`). Configure the actual engine host and required TLS; the manual requires TLS for a secured engine environment. This is a source-backed route, not a complete request/authentication contract. Obtain the target engine contract before mapping or invoking a transaction.
- **SAS OnDemand Decision Engine:** use `onDemandDecisionEngine.addresses`, `inputFormat`, `outputFormat`, `testPing`, and supported `tls` settings (pp. 84–86). JSON input is converted to engine binary and the response converted back unless binary formats are selected. With authorized connectivity testing, `testPing: true` substitutes a hardcoded ping and ignores input messages; it does **not** prove business rules processed the supplied transaction. Restore `testPing: false` for a genuine transaction test.
- **No detection target/contract supplied:** finish mappings, configuration review, and synthetic tests below. Report detection integration as blocked, not as a successful fraud decision or a permanent prohibition on live work.

### Make error behavior explicit

Use `try.processors` for the sequence that must stop after its first failure, then a deliberate error branch after that block (pp. 419–425). Exiting `try` resumes the rest of the flow: it does not terminate the whole flow. `catch.processors` executes for failed messages and clears the error flag when it exits; preserve the needed diagnostic/status before that happens. Alternatively, `switch` with `errored()` can route an error while preserving its flag.

Configure the HTTP input's response status explicitly for failures, using an expression interpolation if needed. The documented default HTTP input response is **200 even when processors fail unless proper error handling is configured** (p. 206). Logging, dropping a message with `deleted()`, or routing an error to a Kafka topic are documented mechanisms, not automatic evidence of rejection delivered to the caller or completed reconciliation.

### Conditional batching, broker, and cache designs

- If batching is needed, use `inputsBatchingPolicy` / `outputsBatchingPolicy` with `byteSize`, `count`, `period`, `check`. Conditions combine as OR and input accumulation spans the flow's inputs. With no batching policy, each input message is a one-message batch (pp. 20–22, 136–139). Ensure batch waiting is compatible with the configured HTTP timeout; do not copy a manual example's timeout as a deployment recommendation.
- `parallelBatch` requires batching and does not guarantee message execution order; do not select it when business order is required. `parallelBranch` runs branch processors concurrently when possible (pp. 87–88).
- If the design requires queued delivery, select a documented broker action and obtain its actual queue/topic, authentication, acknowledgement, persistence, and replay contract before configuring it. A Kafka/AMQP connection is not proof of store-and-forward durability or exactly-once business effects.
- The consulted manual documents local/Redis cache actions, HTTP retries, and broker actions, but these sections do not establish a named stand-in/one-behind feature or a complete store-and-forward guarantee. Do not invent a stand-in switch, fallback decision, persistent queue configuration, or default retry policy from the capability name. Require the applicable target-specific documentation for such a design and use the state-review reference for its acceptance cases.

## 3. Minimal original synthetic walkthrough

Purpose: prove validation, ordered processing, branch-result merging, conditional routing, and caller response without a detection engine, broker, customer data, or external enrichment call. This is a **fixture workflow**, not a fake detection integration or a production fraud policy. The fixed enrichment and route labels are solely test data. The YAML is an authored candidate using the documented configuration surface; it has not been executed here.

Create a project definition with one workload and the following one flow. Do not deploy it merely to read this reference. The `15s` timeout is an arbitrary fixture setting to review in the chosen test environment, not an SLA.

```yaml
id: synthetic-orchestration-check
version: v1
workloads:
  - flows:
      - inputs:
          - httpServer:
              path: /synthetic-transaction-check
              allowedVerbs: [POST]
              timeout: 15s
              syncResponse:
                status: '${! json("responseStatus") }'
                headers:
                  - key: Content-Type
                    value: application/json
        processors:
          - try:
              processors:
                - validateWithJSONSchema:
                    source:
                      schema: '{"type":"object","required":["transactionId","accountToken","amount"],"properties":{"transactionId":{"type":"string"},"accountToken":{"type":"string"},"amount":{"type":"number","minimum":0}}}'
                - branch:
                    requestMap: 'root.accountToken = this.accountToken'
                    processors:
                      - map:
                          mapping: |
                            root.accountToken = this.accountToken
                            root.accountAgeDays = 40
                            root.fixtureOnly = true
                    resultMap: 'root.enrichment = this'
                - switch:
                    cases:
                      - check: this.amount >= 100
                        processors:
                          - map:
                              mapping: |
                                root = this
                                root.route = "HIGH_AMOUNT_TEST"
                      - processors:
                          - map:
                              mapping: |
                                root = this
                                root.route = "STANDARD_TEST"
                - map:
                    mapping: |
                      root = this
                      root.responseStatus = "200"
                      root.outcome = "SYNTHETIC_MAPPING_COMPLETE"
          - catch:
              processors:
                - map:
                    mapping: |
                      root = this
                      root.responseStatus = "422"
                      root.outcome = "INVALID_SYNTHETIC_INPUT"
                      root.diagnostic = error()
        outputs:
          - response: {}
```

Use the synthetic message `{"transactionId":"TX-SYN-01","accountToken":"ACCT-SYN-A","amount":42}`. In an authorized test runtime, send it with POST to the **actual deployed workload URL plus this configured action path**, with JSON content. Do not derive a production host, bearer token, or customer identifier from this example.

Expected success: HTTP 200; unchanged `transactionId`, `accountToken`, and `amount`; `enrichment` containing the same account token, `accountAgeDays: 40`, and `fixtureOnly: true`; `route: "STANDARD_TEST"`; `outcome: "SYNTHETIC_MAPPING_COMPLETE"`; `responseStatus: "200"`. Object-key order is irrelevant. This result says nothing about detection, fraud risk, payment authorization, alert generation, or downstream fulfillment.

| Case | Expected result to verify |
| --- | --- |
| Amount 42 | Success fields above; one enrichment branch; standard route. |
| Amount 100 | High-amount test route; boundary belongs to the first switch case. |
| Missing `amount`, string amount, or negative amount | Validation failure, HTTP 422, invalid-input outcome and diagnostic; no enrichment or route created for that fresh input. |
| Missing `accountToken` | Validation failure before the branch; no synthetic enrichment result. |
| Wrong HTTP method/path | Observe endpoint rejection/routing separately; do not claim the project's validation processor received the message. Exact transport response is target evidence. |

**Artifacts:** saved candidate YAML/revision; toy JSON contract; synthetic input cases; expected/observed response status and semantic payload diff; route/error trace where available; test environment/cadence; explicit list of untested integrations. Remove fixture enrichment and toy schema before real detection integration, replacing them with the actual provider and detection contracts rather than carrying fixture values forward as fallbacks.

## 4. Save, deploy, and verify only when authorized

### UI-managed project

1. A designer opens **Projects → New project**, enters a unique project name and optional description, and chooses **Create** (p. 596). Names allow alphanumerics, hyphen and underscore, up to 255 characters; spaces are not allowed.
2. Enter the candidate definition in **Definition**. Use syntax highlighting, validation/error hover details, and autocompletion. Set name/description and, when needed, **Properties → Short name**. The short name participates in the deployed URL; it supports lowercase alphanumerics and hyphens, up to 30 characters (pp. 594–596).
3. Save and confirm **Validation status: Valid**. **Unknown** requires saving to determine status; **Invalid** cannot deploy. Editor validity does not prove provider connectivity, target schema compatibility, or correct business results.
4. Before the deployer acts, record exact saved revision, environment, all workloads, target security configuration, authorized test cases, expected connected-system effects, and recovery owner. The entire project is deployed, including every workload (pp. 597–603).
5. From **Current Projects**, select/open the project and use **Deploy** (also available on the context menu). Resolve validation/property-rule failures instead of bypassing them. Unsaved changes in the open project disable its deploy button; deploying from the Projects list uses the **saved** version, not unsaved edits.
6. On **Deployments**, refresh and record **Health**, project, environment, workload count, deployed by, date, and deployment ID where displayed. Healthy means every workload has a healthy runtime; it is not proof that a transaction completed detection. If unhealthy, retain diagnostics and contact the system administrator.
7. Obtain the actual workload URL from the environment configuration: ingress access includes environment, project short name, zero-based workload index and action path; in-cluster access uses service name/port and action path (pp. 604–609). Do not guess an API host from the project name. Invoke only the approved synthetic cases and compare the expected artifacts above.

### Administrator-managed worker container

Use the target's supplied deployment material rather than fabricating an installation command. The manual directs Kubernetes deployment to the SAS Viya Deployment Guide and `$deploy/sas-bases/examples/sas-business-orchestration-worker/` README (p. 581); the project is supplied through the worker deployment configuration/ConfigMap (p. 201). A worker runs the first workload by default; `WORKLOAD_ENABLED_BY_INDEX` selects another workload for that container (pp. 202–203).

For UI-managed environments, deployment/property rules and environment setup are described by `$deploy/sas-bases/examples/sas-rfc-solution-workloads/`. These are locators for the administrator's actual distribution, not files required to read this reference.

**Security gate:** provision the actual secrets and mounted certificates before execution. Secret-backed environment interpolation avoids putting credentials in project text (pp. 394–397, 416–418). UI-deployed action certificate files must be in the container's documented `etc/boss/secrets` location; manually deployed actions use their configured mounted location. Do not enable workload `configurations.http.tls` directly for UI deployment; UI HTTP-server TLS requires the environment's deployment configuration. Outbound action TLS remains separately configured. Missing certificates, secrets, privilege, or runtime setup block deployment/invocation, not preparation of the project.

## 5. Operate, change, and recover

1. Monitor the actual workload and its connected-system results separately. Documented worker probe paths are `/internal/live`, `/internal/ready`, `/internal/health`, `/internal/metrics` (p. 207); access them only through the approved monitoring surface. Liveness/readiness is not detection/business acceptance.
2. Configure logging deliberately: `SAS_LOG_LEVEL`, `SAS_LOG_FORMAT`, and workload `configurations.logger.addTimestamp` (pp. 431–434). A workload's level filters its log processors, so a DEBUG processor need not appear under an INFO workload logger. Record correlation/status evidence without copying real transaction payloads or secrets into broadly accessible logs. Authorized Kubernetes operators can inspect pod logs using the documented `kubectl get pods` and `kubectl logs` operations in the correct namespace/context.
3. For a schema/mapping error, preserve the failing synthetic input, processor location, diagnostic, and saved definition; repair the contract/mapping and repeat the affected acceptance cases in the approved environment. A catch that clears an error must not turn an invalid message into a success outcome.
4. For provider/TLS/authentication failure, check actual URL, certificate mount/trust, secret injection, supported authentication and provider contract. Preserve evidence and correct the configuration; do not weaken security or replace enrichment with an invented zero-risk value.
5. For timeout, broker redelivery, late detection response, or downstream uncertainty, use the state/effect ledger in the linked state-review reference before deciding whether to resend. Do not treat transport retry as a recovery plan for business effects.
6. To apply approved project changes, save the corrected definition, select the existing deployment in **Deployments**, choose **Redeploy**, and confirm **Redeploy** (pp. 610–611). It uses the current saved definition; refresh Health and record the updated **Date Deployed**. The manual describes redeployment without loss of service; still verify the specific acceptance cases and resulting state rather than claiming an unmeasured service-level guarantee.
7. For an unhealthy deployment, retain deployment identity, cadence, saved definition and administrator-visible diagnostics, then use the administrator's approved recovery procedure. This source provides redeploy/delete operations, not an automatic rollback/history command. Preserve the approved prior definition before a change if restoration is required by the site procedure; do not invent a rollback button.
8. If retirement is authorized, **Deployments → Delete** removes a deployment. A project cannot be archived while deployed; after removing its deployment, **Current Projects → Archive** moves it to read-only archived projects. **Archived Projects → Restore** returns it to current projects; permanent project deletion requires archiving first (pp. 598–600, 610). These lifecycle actions are not substitutes for reconciling outstanding messages or business effects.

**Return:** exact configuration revision and source cadence; interface/stage table; authorized-effects record; validation/test evidence with expected versus observed results; deployment/health identity if exercised; unresolved integration prerequisites; and the next permitted recovery or promotion action. Label planned checks and unexecuted branches honestly.
