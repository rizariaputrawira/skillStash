# CAS analytical modeling

## Applies-to and consulted evidence

Technical basis: **SAS Visual Statistics: Programming Guide**, cover labeled **2026.01–2026.04***, **April 16, 2026**, [official PDF](https://documentation.sas.com/api/collections/pgmsascdc/v_072/docsets/casactstat/content/casactstat_sas.pdf?locale=en), consulted 2026-10-28, Chapter 22 **Regression Action Set**, `genmod` syntax and **The genmodScore Action**, printed pp.1346–1348. The collection identifier `v_072` and PDF publication date are not installed runtime versions. The asterisk does not establish other releases. The HTML locator [glm action](https://documentation.sas.com/doc/en/pgmsascdc/v_072/casactstat/cas-regression-glm.htm?fromDefault) returned only the Help Center application shell when consulted; substantive technical claims here use the PDF, whose retrieved cover differed from the search result's 2026.02 banner. No blanket release support is inferred.

## Complete modeling sequence

1. Establish actual CAS/server release, analytical entitlement, available action set/action metadata and target-matching official reference. Use the selected authorized CAS client to inspect metadata and result/status conventions; do not invent introspection method names. Record parameter names/types, effect and response options, partition semantics, model storage, scoring output and resource controls. Discovery is not permission to load an action set, train or write tables. Unknown metadata/version blocks unestablished technical operations while leaving the plan below usable.
2. Bind authorized existing train/validation/test CAS tables by **caslib and name**, with ownership/lifetime recorded by the execution client. Check source snapshot, partition ledger, row keys and actual table schemas. Do not assume a SAS libref is a caslib or that a table visible in one session is visible in another. Apply the training-fitted feature recipe consistently; CAS does not make an all-data preprocessing pass leakage-safe.
3. Define a simple baseline and a justified candidate family, response/event mapping and effects. The consulted chapter documents `regression.genmod` for generalized linear modeling and storing a model, and `regression.genmodScore` for restoring/scoring it. It also documents distinct `regression.glm` and `regression.logistic` actions; their parameter and scoring contracts are not interchangeable with `genmod` or with SAS procedures. For classification inspect the chosen action's response options and score columns; do not copy procedure `EVENT=` into a CAS request. For target-free work choose a documented applicable action and stability/utility protocol, not a fictitious target.
4. Prepare each bounded training request with explicit input subset, effects/settings and unused task-owned artifact names. Specify target-supported resource limits; stop conditions do not guarantee wall-clock or memory ceilings unless the target contract says so. Record requested versus observed resource use and every trial's return status, logs, convergence and read/used counts. With authority, execute through the selected client; transport success or an available output table is insufficient proof of successful fitting.
5. Store the fitted model as its documented artifact type. Score validation from that frozen artifact without fitting on validation, preserving row keys and only required columns. Compare baseline and candidates on identical eligible cases and weights. Select on development evidence, then score protected final test under the entrypoint's locked protocol. Preserve transformation identities separately; a stored model need not include external preparation.
6. Check restored/development score parity on keyed approved cases and explicit missing/unseen/near-threshold cases. Report numerical tolerance, schema, row coverage, event mapping, metric calculation and scoring limitations. The guide notes that training diagnostic statistics do not retain their original meaning on nontraining data; its `fitData` flag identifies original fitting data. Do not mark validation/test as fitting data merely to obtain diagnostics. Predicted-value errors on held-out cases can instead be assessed under the declared metric contract.
7. Return model/table/artifact identities, data/transform/source pins, trials, terminal outcomes and evidence. SWAT or the chosen CAS execution owner retains session, submission, table persistence/promotion and cleanup mechanics. Do not promote, replace shared tables, publish, register, or drop resources as an automatic modeling step. Persist/export only within explicit artifact authority and a documented target contract; a CAS model table is not a universal analytic-store or DS2 deployment package.

## Original source-edition-bound regression example

**Not executed.** This request illustrates the consulted **2026.01–2026.04*** guide's `genmod`/`genmodScore` contracts, not compatibility beyond that source scope. Requires an authorized existing CAS session with the regression action set available, caslib `ModelLab`, and separate approved tables `train_ready` and `valid_ready`. Both have unique `case_id`, continuous target `amount` in declared units, and complete numeric `x_ready` transformed with a training-fitted recipe. Data must support fitting a nondegenerate Gaussian identity-link model. Artifact/output names must be unused and task-owned; substitute only approved identifiers.

```sas
proc cas;
  regression.genmod /
    table={caslib='ModelLab', name='train_ready'},
    model={depvar='amount', effects={'x_ready'},
           dist='GAUSSIAN', link='IDENTITY'},
    optimization={maxIter=50, maxTime=30},
    store={caslib='ModelLab', name='candidate_model'};
run;

proc cas;
  regression.genmodScore /
    restore={caslib='ModelLab', name='candidate_model'},
    table={caslib='ModelLab', name='valid_ready'},
    casOut={caslib='ModelLab', name='candidate_valid'},
    copyVars={'case_id','amount'}, pred='prediction';
run;
```

The consulted action syntax defines `maxTime` as maximum CPU seconds; 30 is an illustrative bound, not a service wall-clock guarantee or recommended universal budget. Confirm these exact contracts through target metadata and matching documentation before submission, including the `depvar` spelling used by the guide's examples. Inspect convergence, used observations and the produced prediction column. Compare against the training-mean baseline on the validation rows, computing MAE/RMSE in target units. Score final test only after model choice; its table is intentionally absent from this development request. Repeat scoring of a keyed sample from the same frozen artifact checks repeatability, not predictive validity. Classification needs a separately verified binary/multiclass action specification; it must not use this Gaussian response configuration.

## Not-established cases

Other release compatibility, action availability/entitlements, store durability across sessions, cross-language client argument conversion, export formats and scoring destinations are not established by this example. Capture exact missing evidence; do not call an unknown capability unsupported. If metadata contradicts the consulted source, stop the dependent operation and reconcile against the actual release before choosing a documented alternative.
