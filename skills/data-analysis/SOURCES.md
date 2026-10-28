# Sources and rights boundaries

This package is independently authored question-to-evidence guidance with original synthetic arithmetic and scenarios. Sources were consulted on **2026-10-28** for the claim areas below. No upstream prose, code examples, figures, assets or skill text was copied or adapted. There is no new license grant for this package; upstream documentation/software terms do not automatically license this original text.

## Consulted official documentation

### pandas 2.3.3 documentation

Exact versioned documentation consulted, accessed 2026-10-28:

| Exact URL | Consulted claim area and limit |
| --- | --- |
| https://pandas.pydata.org/pandas-docs/version/2.3.3/reference/api/pandas.read_csv.html | CSV `dtype`, column selection, bounded rows, missing-value token settings and parser parameters; inspected the parameter window, not all engines or formats. |
| https://pandas.pydata.org/pandas-docs/version/2.3.3/user_guide/missing_data.html | Initial missing-sentinel and predicate sections; equality is not a substitute for missing-value detection. Not a comprehensive missing-data inference theory. |
| https://pandas.pydata.org/pandas-docs/version/2.3.3/reference/api/pandas.merge.html | Null-key matching, explicit join keys, match indicators, and cardinality validation; `many_to_many` does not validate uniqueness. |
| https://pandas.pydata.org/pandas-docs/version/2.3.3/reference/api/pandas.DataFrame.groupby.html | Missing group-key exclusion and categorical `observed` behavior in 2.3.3. Defaults may differ in other versions. |
| https://pandas.pydata.org/pandas-docs/version/2.3.3/user_guide/merging.html | Opening merge/join/concat overview only; context for combination operations, not the entire user guide. |

The pandas-specific observations in [profiling](references/profiling-and-evidence.md) are bound to this documentation release. They are not a tested implementation, an installation requirement, a guarantee for a future version, or evidence of the consuming environment's version. No pandas sample code is reproduced. Consult the pandas project's own notices for its material; no upstream license is applied to this package by inference.

### NIST/SEMATECH e-Handbook of Statistical Methods

Edition consulted: **online e-Handbook, the individual sections listed below as served on 2026-10-28**. These pages do not advertise a single numbered software release or immutable revision; their exact historical revision is unknown. The section identifiers are the edition locators, not compatibility pins.

| Exact URL and section | Consulted claim area and limit |
| --- | --- |
| https://www.itl.nist.gov/div898/handbook/eda/section3/eda35h.htm — 1.3.5.17, Detection of Outliers | Distinguish erroneous records from genuine extremes, graphical investigation and distributional assumptions. No numerical outlier threshold is adopted as a business rule. |
| https://www.itl.nist.gov/div898/handbook/prc/section3/prc31.htm — 7.3.1, Do two processes have the same mean? | Independent-group means and unequal-variance/Welch formulas. The package does not require preliminary variance testing as a mechanical selection rule. |
| https://www.itl.nist.gov/div898/handbook/prc/section3/prc311.htm — 7.3.1.1, Analysis of paired observations | Within-pair differences, paired t statistic and independent-pair sample count. Does not establish causality for an uncontrolled before/after comparison. |
| https://www.itl.nist.gov/div898/handbook/eda/section3/eda352.htm — 1.3.5.2, Confidence Limits for the Mean | Mean t interval and repeated-sampling confidence interpretation; not protection against selection or measurement bias. |
| https://www.itl.nist.gov/div898/handbook/prc/section2/prc241.htm — 7.2.4.1, Confidence intervals | Wilson and exact binomial interval discussion; no naive symmetric boundary interval. Does not cover arbitrary dependence or survey weights. |
| https://www.itl.nist.gov/div898/handbook/eda/section3/eda35c.htm — 1.3.5.12, Autocorrelation | Temporal dependence and equal-spacing assumption for the displayed autocorrelation definition; not a universal time-series estimator prescription. |
| https://www.itl.nist.gov/div898/handbook/prc/section4/prc47.htm — 7.4.7, How can we make multiple comparisons? | Repeated pairwise comparisons change overall error; predefinition and multiple-comparison procedures. The package does not adopt language treating failure to reject as proof of equality. |
| https://www.itl.nist.gov/div898/handbook/eda/section3/bootplot.htm — 1.3.3.4, Bootstrap Plot | Resampling with replacement and estimator/distribution limitations. No fixed repetition count is adopted as universally sufficient; dependence-aware resampling requires an appropriate design. |

These statistical explanations inform method selection, not a promise that assumptions hold for a supplied dataset. No NIST examples, datasets, programs or figures are included. Public availability alone is not treated as a license grant; no blanket copyright status for all handbook components is asserted.

### SciPy 1.16.2 Manual

Exact versioned documentation consulted, accessed 2026-10-28; both pages identify manual version **1.16.2**, documentation last updated **2025-09-11**:

| Exact URL | Consulted claim area and limit |
| --- | --- |
| https://docs.scipy.org/doc/scipy-1.16.2/reference/generated/scipy.stats.fisher_exact.html | Opening definition, inputs and null-hypothesis notes: 2×2 fixed-marginal Fisher testing and nonnegative integer counts. Not a default risk-difference interval or paired-data method. |
| https://docs.scipy.org/doc/scipy-1.16.2/reference/generated/scipy.stats.mannwhitneyu.html | Definition, alternatives and method notes: independent samples, distributional rather than automatic median interpretation, ties and exact/asymptotic distinctions. No numeric sample-size cutoff is promoted to a universal rule. |

Used as primary method/implementation documentation, not a required runtime or a claim about installed APIs. No SciPy code or examples were copied. The named methods in [methods and uncertainty](references/methods-and-uncertainty.md) still require the actual tool/version contract before execution; this package supplies no executable SciPy interface.

### Datawrapper first-party visualization guidance

Edition consulted: publisher's online articles as served on **2026-10-28**, not a versioned API specification:

| Exact URL | Publication/revision evidence and claim area |
| --- | --- |
| https://www.datawrapper.de/blog/line-charts | “What to consider when creating line charts,” Lisa Charlotte Muth; published 2018-01-22, page metadata updated 2026-03-10. Time spacing, annotations, explicit projections and axis choices. |
| https://www.datawrapper.de/blog/chart-types-guide | “A friendly guide to choosing a chart type,” Lisa Charlotte Muth; published 2025-06-16, page metadata updated 2026-09-24. Match chart type to the communication question and prefer readable comparisons. |

No poster, image, screenshot, example chart, article text or Datawrapper integration is included. These articles inform independent display guidance; they do not certify accessibility, authorize uploading data or require a chart service. Their online content can change, and update metadata is not an immutable content pin. Publisher rights remain with the relevant rights holders.

## Original synthesis and technical boundaries

The metric cards, reconciliation bridge, cohort/funnel procedure, incomplete-period scenario, output contract and permission boundaries are original synthesis, not an adaptation of an upstream skill. The illustrative formulas use elementary definitions: pooled success counts over pooled eligible counts, explicit weighted sums, absolute/relative change, and matched elapsed windows. Their arithmetic is provided as offline reference expectations, not claimed runtime verification.

No Anthropic plugin or other community skill was consulted or used. No SAS technical documentation was consulted because this package specifies no SAS syntax, product capability or compatibility range. Supplied SAS results may be analyzed offline; product/release-specific technical claims require separate official evidence. Unknown support must remain unknown rather than being relabeled unsupported or universally supported.

The package does not own execution-client lifecycles, model training/deployment or database pipelines, and has no sibling/root dependency. Source review is not a consuming-agent behavior test, host discovery test or live SAS verification.
