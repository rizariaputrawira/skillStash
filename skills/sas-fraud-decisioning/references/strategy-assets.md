# Strategy assets and state

Load for classification/routing, organization/project, message-schema, entity-profile or rule-dependency design. Conceptual basis: supplied deck slides 6–37; see [source limits](../SOURCES.md). This procedure does not certify the deck's topology or terminology for a particular release.

## 1. Resolve scope before logic

Build one row per affected event class: event type/source, classification identifier and hierarchy position, organization, project, schema/revision, eligible rules and evidence locator. Distinguish classification-based eligibility from identity/access authorization. The deck describes organizations as separation boundaries and projects as scoped asset groupings; verify actual isolation and deployment mapping rather than assuming one runtime pod per organization or that project membership alone grants access.

For each boundary, supply a positive intended-routing case and a negative sibling/out-of-scope case. Missing hierarchy, schema or organization evidence blocks the affected routing conclusion. Do not invent automatic inheritance, cross-organization sharing or exact rule-selection precedence.

## 2. Fix the message contract

Record field path, type, identifier representation, unit/currency, null/absent/default semantics, allowed values, array cardinality, source mapping, revision and masking requirement. Distinguish a supplied template from a custom extension. Preserve account identifiers as identifiers, including leading zeros. Check nested arrays and limits against the actual target contract; no universal maximum or supported payment-format parser follows from a slide's format list.

For schema changes, identify producer and consumer revisions, rule/profile/model uses and proposed compatibility checks. Test missing versus explicit null, empty array, invalid type and unknown category. A schema validation pass does not establish business correctness or prove sensitive fields are masked in every downstream record.

## 3. Make profile state explicit

Record entity type, composite key and namespace, source fields, profile variables/types, defaults, update rule/revision, history window and time basis. Specify whether each decision reads pre-event or post-event state. Resolve current-event inclusion, initialization, expiry/reset, late/out-of-order events, duplicate events and concurrent updates from target evidence or owner policy; these are review questions, not claimed product guarantees.

Finish each case with initial state, ordered events, expected state before/after and expected decision. A customer/account/device label is not a unique key definition. Empty profile state is not automatically zero, false or safe to approve. Ensure a profile-derived model feature uses the same point-in-time meaning as training; otherwise mark scoring parity unestablished.

## 4. Bind rule dependencies

For each rule, retain identifier/revision, eligible classification/schema, profile reads/writes, list identifier/version and model score/artifact mapping. Establish evaluation order, short-circuit behavior and competing action precedence from the release contract and approved policy. Separate rule firing, alert creation and transaction decision: one does not prove another.

Record list key normalization, match/miss/unknown outcomes and refresh evidence without assuming Intelligent Decisioning lookup activation semantics apply to Fraud Decisioning lists. Record model score scale, direction, event and missing/error policy; neither a numeric threshold nor a deck example establishes a probability or calibrated risk.

## Result

Return the asset ledger, affected/unaffected routing matrix, schema change impact, state-transition cases, rule/action decision table, and missing target contracts. Use synthetic inputs; no customer identifiers or source code from the deck are needed.
