# Procedure modeling

## Applies-to and claim limits

Technical example basis: **SAS/STAT 9.22 User's Guide**, PROC LOGISTIC [MODEL statement](https://support.sas.com/documentation/cdl/en/statug/63347/HTML/default/statug_logistic_sect016.htm) and [SCORE statement](https://support.sas.com/documentation/cdl/en/statug//63347/HTML/default/statug_logistic_sect021.htm), page metadata dated **2010-06-22**, consulted **2026-10-28**. This is a historical syntax pin, **not** a SAS 9.4, Viya, current Studio, or future-release compatibility claim. Exact installed procedure availability/options must be checked against target-specific official help. No live runtime is established. Methodological steps below are version-neutral.

## Fit and score with a reproducible specification

1. Record the actual SAS execution surface, product/procedure version evidence and matching official reference, authorized input libraries and output destinations. Use the existing selected execution client for sessions/submission; this package does not authorize creating sessions, assigning libraries or installing SASPy.
2. Prepare separate approved training, validation and sealed test inputs using the frozen partition ledger. Materialize transforms under separate authorization, fitting learned values on training only. Record eligible and excluded counts, class/event levels, weights and transformation artifacts. Never fit on a combined table merely because its name includes a partition flag; a procedure must actually use the intended subset.
3. Select a model family and effects from objective/data assumptions: binary/multiclass response, continuous target, nonlinear structure, dependence, distribution and intended interpretation. For regression, specify target units, distribution/link, effects and residual checks; for classification, specify event and level/reference mapping. Verify the chosen procedure's actual family, effect syntax, missing/unseen-level handling, convergence, partition and scoring support. A PROC LOGISTIC example is not a substitute for regression or unsupervised work; the [CAS path](cas-modeling.md) supplies a separate source-bound regression example.
4. Write a complete fit/score plan: input snapshot and partition, frozen preprocessing, response/event, effect list, all nondefault settings, budget, output model identity, validation score output and metric calculation. Fit the baseline then each bounded candidate on training, score validation without refitting, and compare using the entrypoint's metric contract. Store logs and diagnostics including failures, separation, convergence, missing predictions and exclusions; do not suppress them to obtain a winner.
5. After selection, lock model/transform/threshold identities and score final test only under the agreed protocol. If a planned final refit combines development subsets, do it explicitly and preserve the changed model/transform identities. Run restoration/parity checks before handing off artifacts. Do not assume a procedure's persisted model includes externally fitted preprocessing, custom formats or all scoring dependencies.

## Small original, source-edition-bound binary example

**Not executed. Applies only to the documented SAS/STAT 9.22 syntax until target checks establish applicability elsewhere.** Requires authorized, pre-existing `work.train_ready` and `work.valid_ready`: unique `case_id`, unformatted numeric binary `outcome` (0/1), and nonmissing numeric `x_ready`, already transformed with a training-fitted recipe. Both response classes and adequate model-fitting data must exist. `x_ready` is the only input; the target/event, ID and partition fields are not predictors. Output names below must be unused/task-owned and explicitly approved before submission.

```sas
proc logistic data=work.train_ready outmodel=work.candidate_model;
  model outcome(event='1') = x_ready;
  score data=work.valid_ready out=work.candidate_valid;
run;

proc logistic inmodel=work.candidate_model;
  score data=work.valid_ready out=work.restored_valid;
run;
```

The MODEL source establishes explicit binary `EVENT=` and effects; the SCORE source establishes scoring data separately and restoring via `INMODEL=`. With no format, `'1'` is the event label here. With a format, verify the formatted event value rather than copying this literal. SCORE can score without response unless options requiring truth are requested. In this source SCORE is not available with STRATA; do not transplant the pattern into conditional logistic modeling unchanged.

Before submission on another release, verify each statement/option in that release's manual and record the new source pin. After authorized execution, inspect the response profile/event and convergence output, compare actual read/used counts with the contract, inspect the generated probability columns/event mapping rather than guessing their names, and join original/restored outputs on `case_id`. Check row coverage, missing scores, probability domain and declared parity tolerance, then calculate validation metrics from the correct event probability. A model artifact and two written tables alone do not demonstrate a correct fit or parity. The same validation input above tests restoration, not generalization; untouched final test remains separate.

## Blocked and nonapplicable branches

Unknown version or unavailable matching syntax evidence: return the completed plan with exact statement/options needing target confirmation; do not submit the historical example as a compatibility probe. An observed unsupported option requires a supported target-documented alternative or a blocked operation report, not silent fallback. Unsupervised methods need their own documented fit/transform/assignment contract and target-free criteria from [development and evaluation](development-evaluation.md); do not force a response onto them.
