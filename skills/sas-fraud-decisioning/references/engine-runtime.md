# Detection Engine configuration and runtime diagnosis

Load for runtime configuration, Kafka processing, profile-store connectivity, mTLS, package loading, monitoring or response diagnosis. Source: *SAS Detection Engine: User’s Guide*, **2026.03**, March 13, 2026, `v_024-P1:frdod`, printed pp1–40. Full extracted text was returned in recorded source reads; this is not complete visual/semantic incorporation of every source item. Use the package source record and local coverage inventory for remaining review. No configuration or request is executed by reading this reference.

## Architecture and configuration procedure

1. Bind the actual image, namespace, deployment, serving instances, approved configuration and task authority. The guide describes independent primary engines sharing a profile store; checking one engine does not prove all serving builds match. Kafka **3.2.x or higher** and Redis **6.2 or higher** are documented requirements for this edition, not proof of the installed software.
2. Inventory both containers separately. `sas-detection` handles incoming messages, runtime calls, results and metrics; `sas-sda-scr` has separate scoring-sidecar Kafka/Redis settings. Runtime Profile Store, rule-metric Redis and optional Advanced List Redis configuration must not be confused with design-time service credentials.
3. Review the selected nonsecret properties and mount/secret identities against the installed `$deploy/sas-bases/examples/sas-detection` files. Apply only through the authorized deployment process; preserve prior configuration and reconciliation ownership. Do not copy source example passwords, hosts or image tags.

| Contract | Configuration and acceptance |
| --- | --- |
| Kafka input/results/rejects | `SAS_DETECTION_KAFKA_TOPIC`, `SAS_DETECTION_KAFKA_TDR_TOPIC`, `SAS_DETECTION_KAFKA_REJECTTOPIC` identify input, successful-result repository and failed-message topics. The guide's defaults are `input-transactions`, `transaction-detection-repository`, `transaction-reject`. Confirm actual topics and retention; consumption does not delete Kafka history. `SAS_DETECTION_KAFKA_BYPASS` can suppress result writes; consumer enablement and threading are separate controls. |
| Kafka capacity and security | Review `SAS_DETECTION_KAFKA_ACK_BEHAVIOR`, batch/linger/fetch/message byte limits and thread controls together with brokers and ordering policy. `SAS_DETECTION_KAFKA_SECURITY_PROTOCOL` supports `PLAINTEXT`, `SSL`, `SASL_PLAINTEXT`, `SASL_SSL`; SASL mechanisms include `PLAIN`, `SCRAM-SHA-256`, `SCRAM-SHA-512`. Supply approved certificates/keys/trust and secrets; do not disable hostname verification to fix a connection. Defaults are not security policy or an exactly-once guarantee. |
| Processing | `SAS_DETECTION_PROCESSING_SLA` is in milliseconds (documented default85); this is not an observed latency guarantee. `SAS_DETECTION_PROCESSING_OUTPUT_FILTER` selects returned fields; mandatory sets remain request/system/decision/alerted. Empty filter returns the full message, potentially exposing sensitive content. Verbose logging, metric suppression and rule-metric bypass have independent controls. |
| Detection Redis | Bind `SAS_DETECTION_REDIS_HOST`, port, auth user/password, pool size, server domain and TLS trust settings to the intended instance. `SAS_DETECTION_RULE_METRICS_BYPASS=true` skips that container's rule metrics/Redis use, not all signature access by the scoring runtime. |
| Sidecar Redis/list store | `SAS_REDIS_HOST`, port, auth, certificates, TLS, server domain and connection timeout configure profile/signature access. `SAS_REDIS_LIST_HOST` and companion `SAS_REDIS_LIST_*` settings select an additional Advanced List store; otherwise the documented default is shared storage. `SAS_REDIS_TLS` and `SAS_REDIS_LIST_TLS` accept `0/N/F` off and `1/Y/T` on. `SAS_REDIS_PROFILE_COMPRESS` controls written signature compression; coordinate format compatibility. |
| Listener | `SAS_DETECTION_TLS_LISTENER_ENABLED` adds HTTPS **in addition to** HTTP. `SAS_DETECTION_TLS_PORT` defaults8778; reconcile service references if changed. `SAS_DETECTION_MTLS_ENABLED` adds client verification with configured CA/certificate/key mounts. Enabling a TLS listener alone does not establish that plaintext access is unreachable. |

## Secure the actual connection hops

For pod-level mTLS, provision trusted client/server certificate chains and mounted secrets, enable the TLS listener and client verification, then select the installed overlays for Redis/Kafka certificate volumes and ingress termination. The guide distinguishes front-door versus full TLS overlays; pod TLS requires passthrough so client certificates reach the pod. Its OpenShift procedure requires full TLS and route passthrough. Use site-signed material and target-supported controller settings; the manual's self-signed examples are learning material, not production defaults. Verify client rejection without a valid certificate and successful authorized traffic on each intended hop; do not infer broker/Redis encryption from ingress TLS.

## Load packages and process messages

An organization deployment must be loaded by restarting the relevant Container Runtime pod. The guide mentions a restart REST call but does **not** establish its HTTP verb/path/body. Use the installed approved runbook, or an approved automatic restart mechanism; never invent the endpoint.

The watcher checks registry updates at `SAS_DETECTION_WATCHER_INTERVAL_SEC`; zero/nonnumeric disables it and it is disabled by default. Set actual `SAS_DETECTION_DEPLOYMENT_NAME` and explicitly reviewed `SAS_DETECTION_WATCHER_SSL_ENABLED`. The source's tables disagree about the SSL default; do not rely on an inferred default. Observe the restart and loaded build on every serving path.

Kafka input is the documented batch/streaming path. REST/CLI can exercise individual approved messages. Match `message.request.schemaName`, classification and all required fields to the loaded schema. Ion types and Detection's supported subset are different: the source rejects blob/clob/sexp and nested/heterogeneous list combinations, while extra fields may pass through without evaluation. Echoed data therefore does not prove a feature or rule used it. Preserve input/build/state identities, then apply [runtime acceptance](rules-testing-deployment.md#7-execute-authorized-boundary-messages-and-reconcile-results).

## Diagnose without assuming effects

Read `message.sas.system.returnType` before interpreting other generated outputs: **0** means processed; **4** is a runtime error with possible retry; **8** means cannot process. Preserve `returnDesc` and `returnDetails`; for nonzero status, generated variable sets do not prove models/rules ran. Resolve state/effects before any authorized replay.

- `BFLD`/`BVST`: invalid field/set; `MFLD`/`MVST`: missing required field/set; `UFLD`/`UVST`: unexpected field/set.
- `BORG`/`NORG`: classification invalid/missing; `NDEP`: no deployment.
- `NRED`, `NKAF`, legacy-named `NMAS`: runtime dependency connection failures; not permission to switch product/deployment model.
- `IION`: invalid Ion; `IODE`: generic engine error; `ECMD`: command-segment processing error.
- `BBLD`: bad build; the documented engine retries once, so a caller retry could be additional processing.
- `SLAE`: SLA exceeded when the relevant reply-if-timeout behavior is enabled; it is not proof of cancellation or rollback.

Inspect Container Runtime pod logs under authorized access and record minimal sanitized evidence. Monitor evaluated/overtime rule counts, decision/alert recommendation counts, Redis concurrent-write failures and timing histograms with their window/build. The manual contains metric labels/descriptions with apparent count/time inversions and legacy service names; use actual metric metadata/unit and target contract before forming rates or latency claims. Rule-time limits are described in **microseconds**, whereas processing SLA is **milliseconds**. Counters, final decisions, alert creation and fulfilled customer actions are separate evidence layers.

Return applied/prepared configuration, secured-hop checks, loaded identity, message results, metric units/windows, diagnostics and reconciliation actions. Missing target contracts or live authority remain explicit; no runtime result is claimed here.
