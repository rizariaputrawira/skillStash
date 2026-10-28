# Credentials and deployment boundaries — accelerator 2.4.1

Read when configuring provider keys, credential domains, ingress, TLS or MAS/SCR destinations. These instructions are release-specific context for the [2.4.1 tag](https://github.com/sassoftware/sas-agentic-ai-accelerator/tree/2.4.1), not blanket SAS Viya security guarantees. See the [version gate](../SKILL.md#compatibility-gate), [official Managing Credentials guide](https://sassoftware.github.io/sas-agentic-ai-accelerator/Administration-Guide/Managing-Credentials), and [Deployment of Decisions guide](https://sassoftware.github.io/sas-agentic-ai-accelerator/Administration-Guide/Deployment-of-Decisions).

## Provider-key resolution is an access-control decision

Release 2.4.1 describes credential domains for centrally managed per-user or group entries, and definition modes that can source provider credentials from a container environment variable. They have different authorization semantics:

- **Viya credential domain**: establish which user/group identity holds each named entry, who is allowed to retrieve it, and how user versus group credentials override one another. A key being in a Viya domain does not prove a scoring endpoint caller is authorized to use it. Avoid committing `.env`, credential manifests containing values, or command output with values. Use the pinned guide's dry-run/report workflow and review the full desired identity entry set: applying credentials may replace that identity's credential contents, not just add one field.
- **Container environment / Kubernetes Secret**: the key is available to code running in that pod. Confirm only the intended deployment and containers receive the Secret reference, key names match the release definition, logs do not print values, and rotation/restart behavior is understood. A Secret reference protects the manifest from containing plaintext; it does not alone constrain network callers.
- **Caller-supplied `API_KEY` option**: inspect flows and manifested models so the key is not accidentally accepted from untrusted callers or kept in saved options. A supplied non-empty value can override environment-based fallback in the pinned implementation.

The official Model Definition Builder guide and tagged 2.4.1 changelog say the generated ingress has no authentication. This is a critical deployment boundary: anyone able to reach the model endpoint may invoke it using a provider key available to the container. Before exposure, require an explicit access design: restrict/remove ingress or implement an approved authenticated gateway/network policy, scope provider spending, and test from both allowed and denied network identities. Do not claim SAS credential-domain governance controls traffic that bypasses the Viya app.

Credentials files are operational inputs. Verify that `.env` is excluded by the repository's ignore policy and keep populated files in protected secret storage. Do not commit provider tokens, Viya passwords, OAuth client secrets, vector-store credentials, private keys, or populated environment files. Verify that sanitized reports and logs do not contain credential values, cookies, authorization headers, or sensitive prompts.

## TLS and runtime boundaries

Keep HTTPS certificate verification enabled. For custom deployment CAs, configure the actual scoring container's trusted CA bundle and verify hostname/chain. Accelerator docs distinguish CAS/Compute, SAS Micro Analytic Service's Python runner, and SAS Container Runtime environments; a certificate file mounted in one pod/container is not automatically visible in another. Do not set a TLS-verification-off option to turn a trust error into a green check. If a legacy or test exception is explicitly approved, document its exact model/destination and network/data risk; don't generalize it to production.

Before promoting a model, confirm whether the score code makes its own HTTPS request or delegates through a Viya node; then validate the appropriate destination path (MAS versus SCR) and its environment variables. A successful SAS Studio or in-app test may run under a different identity, network path, Python runtime or certificate store than a published model.

## RAG data and retrieval controls

Treat RAG as a data pipeline with its own data rights and access boundary. Before ingestion/retrieval, identify source folders/documents, the user or service identity that can read them, destination vector store and retention policy, embedding/model endpoint, chunking/filter rules, and whether output may contain sensitive source excerpts. Restrict ingestion to approved source scope. Check whether retrieval filters enforce tenant/user authorization at query time; a shared vector store or a prompt instruction is not proof of row/document-level access control. Do not ingest real data as a setup smoke test without authorization.

A RAG document's license and privacy terms, embedding model license, LLM provider terms/retention, vector database license and commercial SAS Viya entitlement are separate. The accelerator license does not confer these rights.

## Required deployment proof

Record the target accelerator tag and Viya release; credential identity/mode **without values**; Secret/domain reference and receiving container(s); ingress/network/auth result for allowed and denied callers; TLS validation and CA file presence in the code-executing container; destination type; sanitized test result; RAG source/access/filter/retention checks where applicable. If no real deployment was examined, state that these boundaries remain unverified.
