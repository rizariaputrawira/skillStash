# Model Definition Builder (`mdb`) — accelerator 2.4.1

This reference is scoped to [SAS Agentic AI Accelerator tag 2.4.1](https://github.com/sassoftware/sas-agentic-ai-accelerator/tree/2.4.1). Its source facts are not generic `sasctl` behavior. See [source record](sources.md) and [version gate](../SKILL.md#compatibility-gate). Current online documentation can move independently; confirm the installed CLI's `mdb --help`, tagged implementation and release changelog before using a command on another release.

## Supported local-to-server pipeline

The accelerator's `mdb` authors model **definitions**; it is distinct from `sasctl`'s general-purpose service/task APIs. Release 2.4.1 CLI uses Python **3.11+**. Its optional `[viya]` extra adds Viya commands; the rest of the CLI can still create, generate, validate, and test local definitions without SAS access. Do not equate local availability with server support.

For model creation, read the [current Model Definition Builder guide](https://sassoftware.github.io/sas-agentic-ai-accelerator/Administration-Guide/Model-Definition-Builder), cross-check it against the pinned checkout, and execute the stages only when each is in scope and authorized:

1. **Add**: `mdb add <provider> <model> --id <definition_id>` creates a definition manifest and assets. Provider names, flags, authentication modes, and support can differ by version; inspect `mdb add --help` and provider-specific schema. A wizard may fetch a live provider catalog or use bundled catalog data. Review every catalog-derived parameter, pricing assumption and model limit before saving. Avoid `--yes` unless defaults were independently reviewed and are intentionally accepted; that flag skips interactive confirmation. Avoid exposing provider keys in shell history.
2. **Generate**: `mdb generate <definition_id>` renders score code and metadata from the manifest. This is a local write. Review outputs, key-handling mode, variable contract, TLS behavior, dependency set and model card before proceeding. If `generation.overrides` or generated files contain edits, protect them using the release's supported override/regeneration rules.
3. **Validate**: `mdb validate <definition_id>` checks local definition consistency; `--live` can call an external provider and may incur cost. The release docs describe `mdb validate --live` as a real provider call and `mdb test` as local execution of generated `scoreModel()` code. Neither is proof of deployment compatibility or production behavior. Use only approved synthetic/non-sensitive inputs and bounded budgets.
4. **Register**: `mdb register <definition_id>` creates/updates a Model Manager model and can touch project/repository metadata. Confirm target environment, project, model/version, and whether operation skips or updates an existing item. `--update` is a write; never assume it merely fills missing fields.
5. **Publish**: `mdb publish <definition_id> --wait` publishes to the configured destination and waits for the documented build. Confirm destination and replacement behavior. Publishing is not the same as deploying a container.
6. **Deploy**: For self-hosted/container models, `mdb deploy <definition_id>` creates or updates Kubernetes deployment configuration and prints/apply instructions; inspect the rendered manifest, namespace, image, ingress, Secret references, resource limits and access restrictions before applying. It is a distinct cluster-admin step.

`mdb ship` combines release-specific stages. Do not use it when a task authorizes only one stage; run explicit commands and verify each boundary. Exact command behaviors and flags must be verified from the 2.4.1 tagged CLI help/source.

## Provider and deployment facts to verify

For external provider models, establish endpoint/API surface, model identifier, allowed options, authentication mode, timeout, retry/response handling and cost controls. A provider API key may be resolved through Viya credentials or placed in a container environment variable according to the chosen definition. These are distinct identity and exposure designs; consult [credentials/deployment](credentials-and-deployment.md). Do not convert secrets into generated source or `options.json` defaults.

For Ollama/vLLM and other self-hosted inference, the deployed container's network path, base URL and resource needs are deployment properties. Verify the endpoint from the actual scoring pod and do not accept a user-controlled scoring option as a trusted server URL. For model weights, review model-card license, tokenizer/code licenses, data terms and redistribution permissions individually; a recipe in this repository does not license the model.

## Observable checks

- **Definition**: exact definition ID/provider/kind; manifest and rendered file changes; input/output names and types; model-specific options and defaults; package/runtime dependency set; no secret literals.
- **Generated code**: local `mdb test` result and sanitized output/error; provider validation result separately; correct scoring response parsing and bounded request behavior.
- **Registration**: returned Model Manager ID/project/version followed by a fresh read; expected score code and metadata are present.
- **Publication**: destination identity; final build/job state and sanitized logs; fresh destination/model read after completion.
- **Container deployment**: reviewed YAML, namespace and resource identity; readiness; TLS and key-secret handling; network policy/auth boundary; a bounded synthetic inference call.

If a stage cannot run, say which evidence is missing. Do not report a file was published because its generated files exist or a job was created because its API returned success.
