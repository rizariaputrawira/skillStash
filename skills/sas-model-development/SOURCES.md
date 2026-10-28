# Sources and rights boundaries

## Authorship

This package is independently authored procedural guidance with compact original synthetic examples. Sources were consulted for factual contracts, not copied as manual text, tutorial code or assets. SAS documentation retains its own copyright and terms; links and factual consultation do not grant redistribution rights in those materials. No new license grant is made for this package. No community or AI-generated knowledge index is an authority here. No credentials, PII or customer examples are included.

All sources below were consulted **2026-10-28**. A consulted edition is evidence for bounded technical claims, not a tested installation compatibility matrix. No SAS execution, host discovery, installation or deployment was exercised during authorship.

|Official source and exact URL|Edition/date actually observed|Used for and limits|
|---|---|---|
|[SAS Viya: Machine Learning User's Guide](https://documentation.sas.com/api/collections/vdmmlcdc/v_033/docsets/vdmmlug/content/vdmmlug.pdf?locale=en)|Cover **2025.10–2026.03***; **March 17, 2026**; collection locator `v_033`|Printed pp.109–113 roles, raw/formatted target ordering, event selection, existing partition mapping versus generated indicator, metadata versus transformation, 2026.01 propagation delta and exceptions; pp.148–149 configured selection statistic and default comparison scope; p.152 onward separates model management/registration/publishing. Not every node option or scoring format; no outside-release support inferred from asterisk or locator.|
|[Partitioning Data](https://support.sas.com/documentation/cdl/en/webeditorref/68839/HTML/default/p1esb7cjl9e1kpn1cluqbddcl1zla.htm)|**SAS Studio 3.5: Task Reference Guide**; metadata **2016-02-16**, release system 9.4|Historical methodological distinction: fitting on training, selection on validation, final assessment on unused test. Not modern Studio UI or general random-split suitability evidence.|
|[PROC LOGISTIC MODEL statement](https://support.sas.com/documentation/cdl/en/statug/63347/HTML/default/statug_logistic_sect016.htm)|**SAS/STAT 9.22 User's Guide**; metadata **2010-06-22**, release system 9.2|Response/effects syntax, binary EVENT= semantics, formatted labels, default ordering and intercept-only possibility. Historical syntax pin, not SAS 9.4 or Viya compatibility.|
|[PROC LOGISTIC SCORE statement](https://support.sas.com/documentation/cdl/en/statug//63347/HTML/default/statug_logistic_sect021.htm)|**SAS/STAT 9.22 User's Guide**; metadata **2010-06-22**, release system 9.2|Separate scoring data/output, restoring model via INMODEL example, response requirements and STRATA limitation. Original package code is deliberately small and unexecuted; probability-column mapping must be inspected. No newer-runtime compatibility claimed.|
|[SAS Visual Statistics: Programming Guide PDF](https://documentation.sas.com/api/collections/pgmsascdc/v_072/docsets/casactstat/content/casactstat_sas.pdf?locale=en)|Retrieved cover **2026.01–2026.04***, **April 16, 2026**; locator `v_072`|Chapter 22 Regression Action Set, genmod parameter syntax, resource controls including CPU maxTime, model storage, and genmodScore restore/score example at printed pp.1346–1348; distinct glm/logistic actions. Not target entitlement, client setup, all action options, persistence or publishing evidence. The source was downloaded because the reader's remote-size limit prevented direct conversion; the local downloaded PDF was read.|
|[glm action HTML locator](https://documentation.sas.com/doc/en/pgmsascdc/v_072/casactstat/cas-regression-glm.htm?fromDefault)|Help Center application shell only when read; search result labeled 2026.02|Discovery locator, not substantive parameter evidence. The PDF cover above is the actually inspected edition; the search banner does not override it or establish installed version.|

## Maintained claim ownership

- `SKILL.md` owns the stable modeling sequence, authority boundary and evidence result.
- `references/development-evaluation.md` owns the independently authored leakage, partition, preprocessing, metric, scoring and offline fixture procedures. These methodological choices are not presented as SAS implementation guarantees or copied standards.
- `references/model-studio.md` owns the consulted edition's UI/metadata/comparison claims and propagation caveats.
- `references/procedure-modeling.md` owns the historical procedure syntax example and target checks.
- `references/cas-modeling.md` owns the consulted CAS action example, artifact distinctions and technical uncertainty branches.

Reopen the owning official reference and record its actual edition before changing a version-sensitive claim. Unknown target information permits methodology but blocks unestablished syntax/operation claims; unsupported requires target-specific negative evidence. Keep source dates, document collection IDs, installed runtime evidence and observed verification distinct.
