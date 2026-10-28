# Packaging, registration, publishing and scoring

Load for model lifecycle work. The API baseline is official **sasctl 1.12.0** documentation observed **2026-10-28**, plus tag `v1.12.0` source. Examples are original, non-live templates; none was executed against SAS. Verify installed signatures, model type and the actual deployment before use.

## 1. Choose the artifact route, not just a function named import

| Starting point | Suitable documented API | Important behavior |
| --- | --- | --- |
| Trained supported scikit-learn estimator or CAS `CASTable` containing an ASTORE | `sasctl.tasks.register_model(model, name, project, ..., X=None, version=None, ..., force=False, record_packages=True, ...)` | A remote workflow, not packaging only. ASTOREs are downloaded/packaged/imported; scikit-learn models are serialized/uploaded with generated score code. Confirm the installed implementation for the specific model type. |
| Complete model directory already prepared | `sasctl.tasks.upload_local_model(path, model_name, project_name, repo_name=None, version="latest")` | Uploads associated files without generating them. Introduced 1.11.3; local ASTORE intake improved 1.11.4. It cannot supply missing score metadata/code for an incomplete directory. |
| Need controlled model metadata, serialization and score-code generation | Public `sasctl.pzmm` classes, then `ImportModel.import_model(...)` | Generate/review assets separately. `import_model` itself is remote and can also write local files; do not call it for a packaging-only request. |
| Already have a valid Model Manager ZIP | `model_repository.import_model_from_zip` | Verify the installed signature, archive schema and target project/version in matching docs/source. Uploading a ZIP does not make arbitrary files scoreable. |
| Need exact existing model version or a specific content operation | `sasctl.services.model_repository` | Use explicit service operations, not a task whose defaults silently select a different version. Model versions and project versions are different resources. |

For `register_model` in the 1.12.0 docs, `X` describes input schema; `input` is deprecated. `version` selects the **project version** and defaults to `new` when unspecified; it is not a request to create an independent version of an existing model. `force=True` can create dependent projects/repositories, and `record_packages=True` captures the local Python package inventory. Review this information before uploading it; do not treat an inventory of every notebook dependency as a verified scoring-runtime lockfile. The task may update project metadata, so even an existing project does not make it a model-only write.

### Review the scoring contract before packaging

Identify the trained object, trusted serializer, preprocessing, predictor columns/order/types, output names/types, event class and probability order, target threshold, missing-value treatment, and dependency/runtime requirements. For classifiers, compare probability columns with the estimator's class order; reversing target labels can produce plausible but wrong scores. The 1.12.0 release changed binary classifier registration to return both classes: do not reuse an older one-output expectation without inspecting generated metadata/code.

Keep packaging in an approved staging directory, not a working directory containing credentials, training data or unrelated assets. Inspect the archive manifest and content roles before upload. Loading pickle/dill or model-provided Python can execute code; only load trusted model assets. Neither registration nor generated requirements establishes safe serialization or runtime compatibility.

The documented `sasctl.pzmm` building blocks include `PickleModel.pickle_trained_model`, `JSONFiles.write_var_json`, `JSONFiles.write_model_properties_json`, `JSONFiles.write_file_metadata_json`, `JSONFiles.create_requirements_json`, `ScoreCode.write_score_code`, and `ZipModel.zip_files`. Select only the required builders and read their installed signatures; some accept in-memory dictionaries while a path can cause filesystem writes. Metadata may include `ModelProperties.json`, `inputVar.json`, `outputVar.json`, `fileMetadata.json`, model serialization and score code; use the target model schema rather than assuming every ZIP has one fixed universal list.

`JSONFiles.create_requirements_json` scans model assets and matches dependencies to versions in the **current** environment. Run it only on trusted assets in the model development environment; another interpreter can generate incorrect versions. Review its result against the actual training/runtime requirements. Its `create_requirements_txt` option was added in 1.11.6 and needs an `output_path` to create the text file. Do not treat a documentation build runner's rendered default `model_path` as a real user path; supply the approved staging directory explicitly.

`ZipModel.zip_files(model_files, model_prefix, is_viya4=False)` packages JSON/model/score assets. A dictionary produces an in-memory archive; a directory path also creates a ZIP there. Set `is_viya4` from verified platform evidence, not from its default. ZIP creation is not registration or deployment.

## 2. Handle Viya 3.5 separately from modern Viya

`ImportModel.import_model(model_files, model_prefix, project, input_data=None, predict_method=None, score_metrics=None, ..., project_version="latest", overwrite_model=False, score_cas=True, ..., overwrite_project_properties=False, target_index=None, preprocess_function=None, **kwargs)` is the documented 1.12.0 shape; inspect the full installed signature for the actual operation.

- `input_data` contains predictor columns only. The documentation describes generated score-code support for int64, float64 and string inputs; do not infer support for arbitrary pandas extension/object/date types.
- Score-code generation requires `input_data`, `predict_method` and `score_metrics`; omitted values can yield a warning and registration **without** newly generated score code. Decide deliberately whether assets are already complete.
- `predict_method` can carry a prediction callable plus output example/type information. Define it from the actual model, not an unrelated template's `predict_proba` dimensions.
- `target_values` must follow the probability order; `target_index` selects the binary success output. The rendered signature default `None` and parameter prose default `1` differ; resolve effective behavior in installed source rather than assuming either.
- `missing_values` and `preprocess_function` change scoring semantics; preserve the trained model's contract instead of enabling imputation as an error workaround.
- On **Viya 3.5**, the documented initial import ZIP excludes Python score code. `score_cas` controls preparation for both CAS and MAS versus MAS-only scoring on that platform. `model_repository.convert_python_to_ds2` is explicitly a 3.5 Python-model wrapper route whose converted models are **not CAS-scoreable**; it is not a modern-platform migration shortcut.
- On the **modern platform**, record actual release/cadence and installed service capability. Automatic version branching inside an import helper is not a certification for every modern release, destination or model framework. If platform detection is unavailable, prepare reviewed local assets but do not choose a 3.5/modern ZIP/score-code branch by guess.
- `overwrite_model=True` can entail deleting an existing model through duplicate handling; `overwrite_project_properties=True` changes shared project metadata. Leave both false unless those precise effects are authorized. A duplicate is not permission to overwrite.

The import helper returns a tuple of server model response and model-files representation/path in the documented API, not merely a model object. Handle its return shape deliberately; do not pass the tuple directly to publishing.

## 3. Register only into the intended project/version

Original non-live template for a trained, trusted scikit-learn estimator and already resolved project, assuming registration and possible project metadata effects are authorized:

```python
from sasctl import tasks
from sasctl.services import model_repository as mr

registered = tasks.register_model(
    trained_estimator,
    model_name,
    project_obj,
    X=predictor_frame,
    version=approved_project_version,
    force=False,
    record_packages=False,  # dependencies are reviewed/supplied separately
)
verified = mr.get_model(registered["id"], refresh=True)
contents = mr.get_model_contents(registered["id"])
```

The caller supplies the estimator, predictor frame and explicit target version; this is not a training recipe. Include needed reviewed dependency assets separately when disabling package capture. Before calling, resolve repository/project by ID and check duplicate policy in that project/version. Afterwards require a non-null fresh model, matching `projectId`, intended version and required serialization/score/metadata content roles. Inspect details with `mr.get_model_details` where needed. Do not claim that file presence proves runnable remote scoring.

For existing model-version workflows, the repository documentation exposes `create_model_version`, `get_model_version` and `get_model_version_contents`; the changelog added version endpoint methods in 1.11.5. Confirm server support and distinguish the original model ID from version IDs. Never translate a project version string into a model version ID.

## 4. Publish to an existing approved destination

Read `model_publish.get_destination(destination_id, refresh=True)` and confirm type/configuration before publishing. Creating a destination or configuring runtimes is a separate administrative write, not an implied dependency to satisfy by default. Do not assume a deployment has a destination called `maslocal`.

For an already registered model, the 1.12.0 task is:

```python
from sasctl import tasks

published = tasks.publish_model(
    registered_model_id, approved_destination_name,
    name=approved_publish_name,
    replace=False,
)
```

This is a non-live **write** template. `tasks.publish_model(model, destination, code=None, name=None, max_retries=60, replace=False, **kwargs)` orchestrates publication and job monitoring. Its return differs by destination: it can be a MAS module with step methods, not a model-repository object. Do not assume a common result schema or identical repository/MAS IDs. MAS names may be normalized; use the server-returned module identity.

Important 1.12.0 source-level constraints:

- With `code=None`, the task calls `model_management.publish_model`; with `code` present it calls `model_publish.publish_model`. The latter service method is marked deprecated since 1.11.5 in rendered docs. Avoid that legacy code-supplied route in new work without a version-specific reason.
- `replace=True` is not a harmless conflict flag: after a failed MAS publication, the task can **delete the module and republish**. Only use it when deletion/replacement of that exact deployed module is authorized.
- The task's `**kwargs` are passed in its code-supplied branch, **not** forwarded in the registered-model branch in inspected v1.12.0 source. Passing `model_version=` through task kwargs is not reliable explicit version selection there.
- When exact model-version publication is needed, use the public `model_management.publish_model(model, destination, model_version="latest", name=None, force=False, reload_model_table=False)` with the explicit approved version after confirming installed/server support. It is a lower-level request: inspect returned links/state and perform documented monitoring; do not call the private `_monitor_job` method as an application API.

Capture the publishing job identity, completed/failed state and sanitized publishing log. Follow appropriate authorized status/self links with bounded waiting. Pending and timed-out are not successful. When a job completes, independently read the destination object/module/table representation using that destination's documented API. A model-repository fresh read only proves registration, not publishing.

`model_repository.copy_python_resources` and `copy_analytic_store` are separate remote copy operations when a documented target path requires them. The docs describe asynchronous per-resource states (`pending`, `copying`, `success`, `failure`). Do not issue these as speculative fixes; when authorized, follow the returned resource states through completion.

## 5. Score through the selected runtime

### MAS: discover the module's actual step contract

Use `microanalytic_score.get_module(module_id, refresh=True)`, `list_module_steps(module)` and `get_module_step(module, step_name)`. A step is not always called `predict`; consult its inputs/outputs before execution. With an authorized, synthetic/non-sensitive input map, the documented explicit invocation is:

```python
from sasctl.services import microanalytic_score as mas

module = mas.get_module(published_module_id, refresh=True)
step = mas.get_module_step(module, approved_step_name)
result = mas.execute_module_step(
    module, approved_step_name, return_dict=True, **approved_inputs
)
```

This invokes remote code and is not assumed read-only merely because it is “scoring.” `define_steps(module)` can bind step names as Python methods, but explicit `execute_module_step` avoids guessing a generated attribute/signature. Confirm required parameter names/types and map returned names to the local prediction contract. `is_uuid` only checks an ID's format; it does not prove module existence, and MAS IDs need not be UUIDs.

### CAS-backed Model Manager score test: not MAS publication

`tasks.score_model_with_cas(score_def_name, model, table_name, table_file=None, ..., server_name="cas-shared-default", library_name="Public", model_version="latest", use_cas_gateway=True, timeout=300, input_mapping=None)` is documented in 1.12.0 and returns a pandas DataFrame. Resolve the approved CAS server/library/table and exact model version rather than accepting those defaults blindly. The task can create score definitions/executions and upload data when `table_file` is supplied: obtain authorization for these writes and data transfer. Do not treat it as a local prediction call or proof a MAS module works.

The 1.12.0 release changed the CAS Gateway default to true and added `input_mapping`; the 1.11.0 changelog says Gateway scoring requires a properly configured environment. Check capability explicitly. Setting `use_cas_gateway=False` selects a different path and should follow documented target support, not conceal a failed Gateway configuration. The custom scoring timeout was added in 1.11.5. Keep a timeout's remote status unresolved until inspected; increasing it does not repair a model/schema error.

For either runtime, compare a tiny approved test set with local predictions using the same preprocessing, feature ordering, class mapping and declared tolerance. Report row count/schema and sanitized comparison, not customer data. State precisely whether evidence covers registration, publishing, MAS execution or CAS score testing; none implies the others.

## Official sources

- Tasks/signatures: https://sassoftware.github.io/python-sasctl/api/sasctl.tasks.html
- Inspected publish orchestration: https://github.com/sassoftware/python-sasctl/blob/v1.12.0/src/sasctl/tasks.py
- Packaging/import branches: https://sassoftware.github.io/python-sasctl/api/pzmm/import_model.html and https://sassoftware.github.io/python-sasctl/api/pzmm/zip_model.html
- JSON/dependency generation: https://sassoftware.github.io/python-sasctl/api/pzmm/write_json_files.html
- Serialization and score generation: https://sassoftware.github.io/python-sasctl/api/pzmm/pickle_model.html and https://sassoftware.github.io/python-sasctl/api/pzmm/write_score_code.html
- Model lookup/content/version/copy APIs: https://sassoftware.github.io/python-sasctl/api/services/model_repository.html
- Destination APIs/deprecation: https://sassoftware.github.io/python-sasctl/api/services/model_publish.html
- Explicit version publication: https://sassoftware.github.io/python-sasctl/api/services/model_management.html
- MAS contracts and execution: https://sassoftware.github.io/python-sasctl/api/services/microanalytic_score.html
- Release-specific behavior: https://github.com/sassoftware/python-sasctl/blob/v1.12.0/CHANGELOG.md

No live runtime compatibility, model accuracy or deployment readiness is established by these sources. See [../SOURCES.md](../SOURCES.md) for provenance and limits.
