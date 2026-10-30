# Sources and provenance

Research date: **2026-10-30**. This package contains independently authored how-to and review procedures, not copied source documentation or product implementation. No repository-wide license is declared for original skill texts. Source links do not transfer source rights or imply SAS endorsement.

## Official manuals supplied locally

The following original procedural synthesis uses user-supplied official SAS PDFs. Locators below are plain provenance records, not links/dependencies required to use the copied package. Source copyright/reserved-rights notices remain applicable; no PDF, manual body, screenshot, sample program or encoded payload is redistributed. Consultation does not establish permission to copy those materials. **Consulted edition is not installed compatibility.**

| Local locator | Actual title / edition / document identity | Consulted use |
| --- | --- | --- |
| `.sources/archive/partially-reviewed/2026.03/SAS Fraud Decisioning - User Guide (2026.03).pdf` | *SAS Fraud Decisioning: User’s Guide*, 2026.03, March 17, 2026, `v_028-P1:frdpayug` | Printed pp.1–2 product/scope; p.109 alert types; pp.111–115 roles/capabilities; pp.143–155 page/message context; pp.183–199 markings, queues, dispositions/reasons, manual-alert prerequisites and output filters; pp.201–208 SFM migration |
| `.sources/archive/partially-reviewed/2026.03/SAS Detection - Administrator Guide.pdf` | *SAS Detection: Administrator’s Guide*, 2026.03, March 17, 2026, `v_032-P1:frdag` | Classifications, access, organizations, models, projects and deployment; exact practical page ranges are recorded in the consuming references |
| `.sources/archive/partially-reviewed/2026.03/SAS Message Schema - User Guide.pdf` | *SAS Message Schema: User’s Guide*, 2026.03, March 13, 2026, `v_030-P1:frdmsg` | Printed pp.1–4, 19–20, 27–45, 47–61: access/reuse, schema and variable creation/versioning, profile keys/variables/sharing and change limits |
| `.sources/archive/partially-reviewed/2026.03/SAS Detection - Rules Authoring User's Guide.pdf` | *SAS Detection: Rules Authoring User’s Guide*, 2026.03, March 13, 2026, `v_033-P1:frdrs` | Rule creation, assisted/coded paths, variable/profile rules, tests and lifecycle; exact consulted pages are recorded in the rules reference |
| `.sources/archive/partially-reviewed/2026.03/SAS Detection Engine - User Guide.pdf` | *SAS Detection Engine: User’s Guide*, 2026.03, March 13, 2026, `v_024-P1:frdod` | Engine artifact/loading and restart boundaries; exact consulted pages are recorded in the rules reference |
| `.sources/archive/partially-reviewed/2025.05/SAS Detection CLI.pdf` | *SAS Detection Command Line Interfaces*, 2025.05, May 12, 2025, `v_012-P1:frdcli` | Conditional Detection CLI procedures only; not assumed to document the newer SFD-specific output-filter CLI |
| `.sources/archive/partially-reviewed/2026.03/SAS BOSS - User Guide.pdf` | *SAS Business Orchestration Services: User’s Guide*, 2026.03, May 5, 2026, `v_032-P3:eopug` | Flow/configuration and worker/runtime procedures; exact consulted sections/pages are recorded in the BOSS reference |
| `.sources/archive/partially-reviewed/2026.04 - 2026.05/SAS Visual Analytics - Designing Reports.pdf` | *SAS Visual Analytics: Designing Reports*, 2026.04–2026.05, May 2026, `v_019-P1:vareports` | Printed pp.7–10 Copilot conditions, pp.29–32 report/data/object/page workflow, pp.46–49 save/view/refresh and copy-delivery limitations |

The differently named `.sources/archive/partially-reviewed/2026.03/SAS Detection Artchitecture - User Guide.pdf` has the **Rules Authoring** cover and is byte-identical to the correctly named rules guide. It is not evidence of a second architecture manual.

### Manual integrity records

| Manual | SHA-256 |
| --- | --- |
| SFD User’s Guide | `7d72688cfa6f2977cd244c72222df7aba32d03eae81b9048d3ef52319949d024` |
| Detection Administrator | `e6d8263690030ef68c7523183b7a35d8b288dabf58a88b901ef11521403c5236` |
| Message Schema | `53106aeea32159ba4382c89e67570e3f255ff05e7041ca8fe575ff8027cb6200` |
| Rules Authoring, including misnamed duplicate | `665b70a1465b111772ef4597574d9c1b042bb1a73f6d162b12e04dfc836207fb` |
| Detection Engine | `6fa95654993633eeb2fb77362795a82e06dacfe3199c4eab4039b2dcf5fd16f3` |
| Detection CLI | `a30ff4939b4f26f0f18059318db74bbc8f8c1f237f84781812b7d6b1e6444564` |
| BOSS | `115a7500a113769f6deb47ed1a068365e8e574edfab1625b717b5dcd9e3937b4` |
| Visual Analytics report design | `4c097ea92ff30c55290790b0641abab2e178605180e6f92f5b46bc95f7767553` |

Source extraction is not assumed complete. In particular, SFD user-guide extraction flags incomplete physical pages 3, 4, 5, 9, 105, 113, 115, 133, 147, 201, 205 and 211; report-design extraction flags pages 1–3. Procedures use the readable relevant text, not inferred screenshot controls. PDF internal/mock HTTP hostnames and malformed extracted example payloads are not runnable contracts; migration guidance requires the actual approved service contract. The SFD guide cross-references a newer *SAS Fraud Decisioning Command Line Interfaces* for output-filter configuration; the supplied older Detection CLI does not establish those commands.

### Coverage is not a used flag

The earlier consulted-page records above describe the initial enrichment, not every page of those manuals. A subsequent pass returned the full extracted text of the eight distinct PDFs and inspected selected page images, but interrupted review work did not complete a per-item incorporation signoff. Additional functional references now cover administration, Python model/signature packaging, schema/Core/type checks, coded-rule contracts, runtime/CLI families, BOSS actions/expressions/schema validation and report operations. Those additions do not imply every source table, example, visual or configuration parameter is incorporated.

The local `.sources/archive/coverage/` inventories retain observed text-read ranges, heading/table-item locations, successful/failed page-image inspections and remaining semantic-disposition requirements. Text returned by a tool, facts reviewed, facts incorporated into a skill, and runtime verification are separate statuses. All ten previously “used” files (including one duplicate) remain **partially reviewed**. No file receives a fully reviewed status until every substantive item and visual/media component is accounted for and each useful contract maps to verified guidance or an explicit source-bound limitation.

The enablement deck's text/notes and attached images/media were inventoried; selected screenshots and video frames were inspected in the subsequent pass. Video sampling is not full temporal or audio review, and no reconstructed full-slide layout or complete media signoff is claimed. The initial extraction-only observations below remain historical evidence for the original references, not a description of every later inspection.



## Supplied enablement presentation

- Supplied locator: `.sources/archive/partially-reviewed/Others/SAS FD - High-level Enablement.pptx` (repository-local research input, not a package dependency or distributed attachment).
- SHA-256: `009aab6a79d48cb93220a12f9ae464f33990c04696b2b082368248e621401b24`.
- 67 slide XML parts. File metadata records creation **2026-05-29** and modification **2026-06-01**; these are document metadata, not a product release. No installed Fraud Decisioning version was established.
- Read slide text and available speaker notes by extracting XML. The document converter failed on a 94.1 MB embedded MP4, so no media was extracted, watched or executed. Screenshot-only walkthroughs, diagrams' spatial relationships and visual rule-state arrows were not verified. Speaker-note page numbers are not used as slide locators.

| Source slides | Use in original guidance | Limit |
| --- | --- | --- |
| 2–4 | Separate detection, orchestration, modeling, investigation and reporting surfaces | Solution overview is not proof every component is installed |
| 6–26 | Classification, organization/project and message-schema review questions | No universal access, isolation, pod topology or schema compatibility guarantee |
| 27–37 | Entity-profile state and rule dependency review | Illustrative rule body and numeric score threshold are not copied or endorsed |
| 38–42 | Rule-phase versus deployment-evidence distinction | Exact transitions and UI behavior remain release-bound |
| 43–50 | Alert triage, feedback, enrichment and orchestration boundaries | No BOSS API, delivery guarantee, timeout default or customer-action authority inferred |
| 53–56, 59–62 | Separate model handoff/governance and rule impact evidence | Walkthrough/report titles alone do not establish implementation or metric formulas |
| 63–67 | Roadmap/current-product separation only | Slide 65 explicitly labels planned concepts subject to change; not a deployment capability claim |

The deck includes SAS copyright/all-rights-reserved notices. No redistribution/adaptation license was established. No slide bodies, rule code, screenshots, embedded media, diagrams or customer data are incorporated. The source is used to identify conceptual review needs; all procedures are original. Retain local source files privately according to applicable rights; the skill works without them.

The separately supplied `SAS FD - High-level Enablement - Additional Slide.pptx` was not readable as a PPTX ZIP by the document reader; `file` identified a Composite Document container. The user described protection and authorized skipping it. It was skipped without bypass attempts, and none of its content supports this package. Container identification alone does not establish the precise protection mechanism.

## Official SAS web corroboration

Only official SAS sites were consulted online for this enrichment.

| Source | Observed scope | Applicability limit |
| --- | --- | --- |
| [SAS Fraud Decisioning product page](https://www.sas.com/en_us/software/fraud-decisioning.html) | Real-time profiling/scoring, orchestration, rule strategy testing and alert triage; current page also describes Copilot-assisted rules | Moving, unversioned product overview read 2026-10-30; not a service contract, installed entitlement, performance measurement or release compatibility matrix |
| [SAS Fraud Decisioning Rules Writer guided path](https://www.sas.com/en_us/learn/sas-fraud-decisioning-writer.html) | Detection Architecture, profiles, Advanced Lists, writing rules, rule phases and deployment are distinct learning topics | Landing-page text only; linked videos/courses were not consumed and exact UI or phase behavior was not verified |
| [SAS Fraud Decisioning Administrator guided path](https://www.sas.com/en_us/learn/sas-fraud-decisioning-admin.html) | Detection design/runtime, resources, orchestration and alert administration are separate learning surfaces | Landing page read; linked videos/courses not consumed |
| [SAS Fraud Decisioning Support](https://support.sas.com/en/software/fraud-decisioning-support.html) | Official discovery links for SFD, Detection, Alert Triage, BOSS and related products | Moving support page, not a release-specific feature or entitlement matrix |

The official SAS Community articles [Creating Rules](https://communities.sas.com/t5/SAS-Communities-Library/Creating-Rules-in-SAS-Fraud-Decisioning/ta-p/973701), [Building a Profile](https://communities.sas.com/t5/SAS-Communities-Library/Building-a-Profile-with-SAS-Fraud-Decisioning/ta-p/973727), [Configuring Advanced Lists](https://communities.sas.com/t5/SAS-Communities-Library/Making-a-List-and-Checking-it-Twice-Configuring-Advanced-Lists/ta-p/954362) and [Manual Alert Creation](https://communities.sas.com/t5/SAS-Communities-Library/Configure-Manual-Alert-Creation-in-SAS-Alert-Triage/ta-p/986398) returned **HTTP 403**. Search snippets are not authority for technical claims. No access restriction was bypassed.

The support page's public Help Center links resolved to SFD `frdpaycdc/v_035/frdpayug`, Detection `frdcdc/v_041/frdrs`, Alert Triage `alrttrgcdc/v_011/alrttrgug` and BOSS `eopcdc/v_038/eopug`. Static reads returned application shells; attempts at their PDF endpoints did not yield PDFs. A rendered browser check of the SFD page also showed no substantive content. These locators were not used as current technical evidence; the supplied local manuals are the consulted editions. A resolved current locator does not replace a local manual's cover/footer version.

## Verification boundary

Offline document review and synthetic exercises cannot establish native host discovery, product support, authentication, actual organization isolation, BOSS delivery, rule compilation, profile concurrency, alert fulfillment, runtime deployment or fraud outcomes. Obtain target-matched documentation and authorized observed evidence for those layers. A public Copilot feature description does not validate every planned deck concept or warrant another Agentic AI Accelerator execution path.
