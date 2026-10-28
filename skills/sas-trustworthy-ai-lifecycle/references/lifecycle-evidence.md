# Lifecycle evidence templates

Adapted from **SAS Trustworthy AI Life Cycle**, SAS Institute Inc. / sassoftware, [documentation at commit 40e4ede91cffe856b0d6e7bb50f688594d9ed1d0](https://github.com/sassoftware/sas-trustworthy-ai-life-cycle/tree/40e4ede91cffe856b0d6e7bb50f688594d9ed1d0/website/docs), licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/). This adapted file is provided under CC BY 4.0. Changes: condensed the project/data/test/deployment/monitoring questionnaires into evidence tables; added explicit unknowns, revision binding, exception records and agent-versus-owner boundaries. No SAS endorsement is implied. Retain this attribution, license link and change notice with redistributed adaptations. Detailed source mapping: [SOURCES.md](../SOURCES.md).

Use the sections needed for the assignment. These are blank output recipes, not completed approvals or evidence of a passing project.

## 1. Model-governance evidence record

Start with project/model ID and revision, stage, intended use and prohibited uses, deployment destination, review date, evidence cutoff, data and policy revisions, reviewer, and assigned owners. Describe benefits, harms, affected people, assumptions, limitations and the existing baseline decision process.

Create rows for the applicable evidence families below. For each row supply: **criterion | accountable owner | evidence locator and revision/date | status | gap or failed result | next action**. Status is evidenced, missing, failed, accepted exception, or not applicable. Exceptions require the criterion, actual result/gap, authorized acceptor, rationale, conditions and expiry/review date; an exception does not convert a failed test into a passed test. Not applicable requires a reason. Missing owner decisions remain missing.

| Evidence family | What the record must establish |
| --- | --- |
| Ownership and use | Named model, risk, development, deployment, data and domain owners; purpose, users, assumptions, limitations and feedback route |
| Data fitness and privacy | Data provenance and permitted use, quality/coverage assessment, privacy risks and controls, documented variables of concern and responsible-owner review |
| Model development | Reproducible model/data revisions, comparison baseline, chosen metrics, model limitations and champion/challenger rationale where applicable |
| Testing | Agreed strategy and acceptance criteria, test-data revision, executed procedure, measured results and unresolved failures |
| Risk and approval | Documented harms/risk tolerance, mitigation evidence and identifiable owner decisions; no approval inferred from a checked box |
| Deployment | Destination and selected revision, integration contract, validation procedure and observed result, unresolved integration risks |
| Monitoring and review | KPI definitions, thresholds, cadence, evidence retention, accountable reviewers and response to breaches |

## 2. Production-readiness review

Record **criterion | acceptance rule and owner | evidence/result | disposition | unresolved action** for:

1. The exact candidate revision, intended use and accountable owners are identified.
2. Data, privacy and model limitations have been reviewed against the agreed use.
3. The documented test strategy has actual results; failures and exceptions are visible.
4. Model approval is recorded with approver, date, revision and conditions, or explicitly absent.
5. The deployment plan names the target and business-relevant validation inputs/expected outputs. Record deployment validation separately as planned, executed with evidence, or failed; predeployment review cannot claim a successful future deployment.
6. Monitoring ownership and breach-response decisions are assigned.

Conclude with **ready for the specifically named next stage**, **not ready**, or **insufficient evidence**, and explain why. Keep owner authorization separate from the agent's recommendation. A predeployment readiness recommendation is not approval of untested production behavior. An accepted exception must identify who has authority to accept it and any conditions that still prevent release.

## 3. Monitoring and escalation plan

Provide one row per KPI: **name/purpose | definition and population/window | data/label source and availability | threshold and approving owner | evaluation cadence | reporting/retention destination | accountable reviewer | breach action and escalation | evidence of execution**.

Document who reviews thresholds and when, how an unavailable or stale measurement is reported, and how incident findings feed back into data preparation, model development, champion selection or retirement. A plan is not an active schedule: identify the actual scheduler/workflow and its authorization only when implementation is requested. For each completed monitoring cycle, retain the evaluated revision/window, result and action taken. Do not describe monitoring as running without execution evidence.
