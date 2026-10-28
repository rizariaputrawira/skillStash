# Model Manager workflow boundaries

Load when asked to use or automate the SAS Trustworthy AI Life Cycle workflow, not for a document-only review.

The [SAS workflow sample README](https://github.com/sassoftware/model-management-resources/blob/6707023b6f1d43264a65a24cd974e04537296b67/samples/Workflow_Integration/Trustworthy_AI_Life_Cycle/README.md) states that workflow **2.0.0** is delivered with SAS Model Manager beginning in **Stable 2024.09**. Earlier beta archives are described as experimental. This is evidence about the sample, not proof that a particular installed release contains or supports the same definition.

## Inspect before proposing changes

1. Confirm the actual product entitlements, Model Manager and Workflow Manager availability, platform release/cadence/patches, installed definition name/version and existing project workflow instances. Inspect the delivered definition before proposing to import a second copy. Unknown availability blocks activation, not preparing a review.
2. Compare the target's release-matched documentation and delivered workflow to the sample. The documentation lifecycle and workflow are not identical; do not claim that importing the workflow implements every checklist item. This workflow targets a **Model Manager project**, not arbitrary Fraud Decisioning case, transaction or rule orchestration.
3. Record the workflow client configuration, execution identity and required permissions. The sample describes administrator-only activation, an administrator starting the workflow, and administrator access for the model owner to monitor instances in Workflow Manager. Validate target-specific requirements with the administrator; do not grant broad administrator access or register a client as an implicit setup step.
4. If import/activation is explicitly authorized, inspect the selected artifact and its contents/terms first, preserve the existing definition/version and affected-instance information, and plan changes using the actual service contracts. Importing, version creation, activation and starting an instance are distinct mutations. Do not import an archive merely to read this skill.
5. Before enabling automation, review performance definitions, KPI alert rules, timer settings and alert destinations against the approved monitoring plan. Sample timer defaults are not fraud-monitoring requirements. Optional Teams notification setup introduces a separate integration; protect webhook URLs as credentials and do not copy them into governance evidence.
6. Verify an authorized workflow using the intended project, definition version, task assignments and observed transitions/results. A definition import or active flag is not proof that performance monitoring executed. If no environment or artifact inspection is authorized, return prerequisites and an offline plan, not a claim of successful integration.

The ZIP/BPMN/companion document contents were not inspected or bundled for this skill, and no workflow was imported or executed. See [source coverage](../SOURCES.md).
