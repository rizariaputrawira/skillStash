# Validate BOSS project contracts

Load when validating a project definition, selecting a schema/editor integration, resolving an unknown action/property or distinguishing configuration validity from runtime acceptance. Source: *SAS Business Orchestration Services: User’s Guide*, **2026.03**, May 5, 2026, `v_032-P3:eopug`, chapter12 printed pp437–580, with chapters3 and6–8 for interpretation. The printed schema's extracted text was inspected, but its complete definitions and every parameter have **not** been incorporated or independently validated here. The local coverage ledger retains that unfinished reconciliation. No upstream schema body is redistributed.

## Obtain the right schema, not a reconstructed PDF example

Use the matching installed BOSS editor/schema for the product cadence and action versions. The guide describes JSON Schema draft07, with descriptions, types, required/optional properties, enums/defaults and examples. The printed extraction contains pagination, broken URLs, wrapping and apparent inconsistencies: it is not a trustworthy machine-readable schema obtained merely by copying the extracted text.

Normally use BOSS **Definition** editor validation. If an explicitly authorized task instead uses VS Code, the manual describes an approved YAML validation extension and a workspace `yaml.schemas` association between the actual schema file and selected YAML patterns. Do not install an extension, overwrite workspace settings or fetch arbitrary `$ref` resources merely to read this skill. Use trusted/local schema resources and review validator network behavior. Validation is preparation; project save, deploy and invocation are separate effects.

## Check structure before interpreting defaults

1. Parse YAML and reject malformed indentation, repeated/conflicting keys or unintended scalar types using the selected parser's documented behavior. Parsing alone does not validate BOSS expressions or the product contract.
2. Check project identity/version/workloads and each workload's configurations/flows. Each flow needs supported input/output actions and its documented processor structure; an empty processor list is different from a missing required property. Workload/project versions are not the SAS cadence.
3. Validate each action wrapper against its actual branch. `oneOf` means the intended alternative must match under that schema's semantics; sibling action keys, misspellings or extra properties can fail where `additionalProperties:false` is used. Do not assume all nested objects reject extras identically.
4. Resolve `source` alternatives for schemas and action-specific required values. A type-correct URL/path does not establish that the resource exists, is authorized or has the expected version. An example value is not a production default.
5. Apply defaults deliberately only where documented; schema validation does not universally insert defaults. Check whether a property permits static text, startup environment interpolation or per-message expression interpolation. A syntactically valid string is not proof an embedded query can evaluate successfully for missing/wrong-type data.
6. Inspect batching `byteSize`, `count`, `period` and `check` together. Source byte/count zero disables that threshold; multiple configured conditions flush when any is met. Empty thresholds and batch waiting must be reconciled with transport timeouts and ordered-state requirements.

## Definition families to reconcile

The chapter covers processor families for conversion (ISO/Ion/JSON/Avro/XML), mappings/branches and parallelism, try/catch/switch, validation, caches, HTTP/SQL/Kafka/SMTP, grouping/pack/unpack/sleep, OnDemand Decision Engine and experimental Visual Investigator operations. It also contains watchlist-screening/management definitions that refer to a separate SAS Real-Time Watchlist Screening API. Their presence in a shared schema is **not** an SFD entitlement, approved sanctions policy or a complete screening request contract.

Workload definitions cover HTTP/logger/content templates; input definitions include HTTP, Kafka, file/socket/socket-server, AMQP, AWS S3, Azure Blob and generation/scanners; output definitions include response/drop/stdout, file/socket, HTTP, Kafka, AMQP and cloud storage. These families require their own full property/enum/constraint reconciliation before claiming comprehensive coverage. Use [action selection](boss-actions.md) for prepared patterns and [expression language](boss-expression-language.md) for query behavior—not a fabricated universal action schema.

## Verify the layer actually exercised

Retain exact schema/cadence identity and digest, parser/validator/version, configuration revision, errors with instance/schema paths, approved interpolations and unknown properties. Test a known-valid synthetic project and controlled invalid cases such as a misspelled action, missing required property, wrong type and conflicting alternatives. A validator pass establishes only that validator/schema combination, not destination connectivity, secret mounting, data masking, ordered execution, delivery guarantees or business correctness.

After authorized save/deploy, separately observe editor validation, saved-versus-unsaved revision, deployment health and actual synthetic message outputs. Resolve source/schema/runtime disagreements explicitly; do not edit a local schema to suppress a product validation error or claim unsupported compatibility. Until every remaining source definition and visual/example ambiguity is dispositioned, the manual remains **partially reviewed** even when this workflow is usable.
