# Rule code: detection methods and supported DS2 subset

Load when writing coded rules, inspecting assistant-generated actions, choosing an array/function/format, or diagnosing a rule-method mismatch. This reference owns code-level contracts; use [rules-testing-deployment.md](rules-testing-deployment.md) for lifecycle, approval and evidence, [advanced-lists.md](advanced-lists.md) for list maintenance/visibility, and [configure-assets.md](configure-assets.md) for actual field paths and profile ownership. No SAS execution, installation or live authority is implied.

## Source and applicability

Original synthesis of **SAS Detection: Rules Authoring User’s Guide**, **2026.03**, March 13, 2026, `v_033-P1:frdrs`: printed pp24–29, Appendix 1 pp72–100 and Appendix 2 pp101–111. Use [SAS Help Center](https://documentation.sas.com/) to select the exact release/title; the document refers to *SAS DS2 Language Reference* and *SAS DS2 Programmer’s Guide* for general syntax. Those external references were not reviewed in this pass and cannot enlarge Detection’s supported subset. Exact API/function/format identifiers below are factual locators, not copied manual programs. No source examples are offered as runnable code.

Text for the whole manual was inspected, including every appendix table and example. Physical-page rendering failed in this review environment. Detached table-cell limitations on printed p104 and garbled data-type layout on p24 remain explicitly source-bound below; screenshots, diagrams and visual-only controls are not certified by the text review. These limits matter before claiming complete source coverage, not merely when executing code.

## 1. Bind types, scope and supported statements

Before writing code, obtain the actual schema variables and their read/write access, array bounds, model/list names and key signatures from the target editor. Record units, timestamp basis, missing-value handling and identifier preservation. The manual maps BIGINT to big integer, DOUBLE to currency/number/timestamp, INT to Boolean/integer, and VARCHAR(N) to string; Boolean is 0 or 1. Missing values assigned to types other than DOUBLE or CHAR become null. Numeric-only strings can be implicitly converted for calculations: do not rely on this for account identifiers or leading zeroes. [pp24–25]

Incoming types are a restricted subset, not arbitrary DS2 objects. The p24 extraction interleaves BIGINT/DOUBLE range notes; do not assert exact BIGINT/DOUBLE precision from that layout without inspecting the source page. INT range is stated as −2,147,483,648 to 2,147,483,647. Scientific notation and numbers ending in `N`/`n` are disallowed in this rule context. The stated VARCHAR ceiling is 10,485,760 characters (described as 42 MB); a field’s actual schema length can be much smaller. Local code-editor arrays have 1–32,767 elements, unlike schema-defined arrays’ own limits. [pp24–25]

Supported statement families are assignment, DECLARE, DO, IF-THEN/ELSE, SELECT, CONTINUE and LEAVE, plus the documented `detection.*` actions. Appendix 2 says “any detection.* macro”; interpret that within the documented methods and rule-type permissions, not as authority for an invented method or arbitrary SAS macro/PROC, I/O, package or SQL call. Unsupported constructs prevent Coding → Testing promotion. Operator precedence is delegated to the release-matched DS2 reference; parenthesize the approved condition explicitly. [pp26,72,108–109]

**Finish:** typed input/output/state contract, exact supported constructs, and any unresolved type/precedence questions. Saving validation is necessary but is not runtime proof.

## 2. Choose an action without conflating effects

Variable rules expose `detection.Debug`, `detection.Fire` and `detection.hasRuleFired`; decision actions are not available by changing their spelling. List access for variable rules is separately documented on p12 even though Appendix 1 lists `contains`/`lookup` under decision methods. `scoreIndex` is documented in Appendix 1 but absent from the shorter decision-method list on pp25–26. Preserve these documentation differences; use actual target editor support and validation instead of treating either list as a universal compatibility matrix.

In the signatures below, bracketed fields are optional **positional** arguments, not literal syntax. `code` is a site-defined prioritization code of up to four characters; it is not the numeric decision outcome or a general free-text reason. Empty-string code placeholders are illustrated when later arguments are supplied. Obtain the approved site ordering; alphabetic or numeric comparison of arbitrary codes is not established here.

| Method | Arguments / effect | Source |
| --- | --- | --- |
| `detection.Alert()` | Decision-only alert recommendation; does not itself confirm downstream alert creation. Configure the alert type first; optional alert reason is limited to 765 characters. Rule policy can prohibit Alert/Hold even if typed in code. | pp23,74 |
| `detection.Hold([code])` | Refer outcome 30 plus alert recommendation, equivalent in stated effect to Refer followed by Alert; not evidence of a payment/account hold being executed. Needs permitted alert configuration. | pp23,89–90 |
| `detection.Approve([code],[date],[time])` | Outcome 10; no-argument call corresponds to Authorize(10). | pp75–76 |
| `detection.Verify([code],[date],[time])` | Outcome 20; no-argument call corresponds to Authorize(20). | pp97–98 |
| `detection.Refer([code],[date],[time])` | Outcome 30; no-argument call corresponds to Authorize(30). | pp93–94 |
| `detection.Decline([code],[date],[time])` | Outcome 40; only supersedes previous outcomes when more restrictive; no-argument call corresponds to Authorize(40). | pp82–83 |
| `detection.Pickup([code],[date],[time])` | Outcome 50; no-argument call corresponds to Authorize(50). | p91 |
| `detection.Exclude([code],[date],[time])` | Outcome 60, named `ignore` in generic outcome values; no-argument call corresponds to Authorize(60). | pp84–85 |
| `detection.Approve_Until([code],[days],[seconds])` | Outcome 10 and relative retention arguments; source example’s stated duration conflicts with its numeric seconds. | pp76–77 |
| `detection.Verify_Until([code],[days],[seconds])` | Outcome 20 with relative retention; source example misspells the receiver as `detecion`. | pp98–99 |
| `detection.Refer_Until([code],[days],[seconds])` | Outcome 30 with relative retention. | pp94–95 |
| `detection.Decline_Until([code],[days],[seconds])` | Outcome 40 with relative retention and restrictive-decision rule. | pp83–84 |
| `detection.Pickup_Until([code],[days],[seconds])` | Outcome 50 with relative retention. | p92 |
| `detection.Exclude_Until([code],[days],[seconds])` | Outcome 60 with relative retention. | pp85–86 |
| `detection.Authorize(outcome,[code],[date],[time])` | Decision-only, returns 0 for success or −1 for failure; combines outcome/priority/retention as described below. | pp77–79 |
| `detection.Authorize_Until(outcome,[code],[days],[seconds])` | Most-restrictive outcome plus relative retention; its tie-date policy differs from Authorize’s prose. | pp79–81 |
| `detection.Exit_All_Rules()` | Exits current rule, skips remaining **decision** rules, replies, then processes next message. Does not undo earlier variable/state changes or represent skipping every phase. | pp86–87 |
| `detection.Ignore_Recommendation()` | Decision-only: returns original authorization-system decision as the decision to follow; Detection’s decision is supplemental. Does not erase a recommendation or stop subsequent rules. | pp90–91 |

### Retention and outcome arbitration require exact method evidence

Outcomes are 0 unknown, 10 approve, 20 verify, 30 refer, 40 decline, 50 pickup and 60 ignore/Exclude. Most restrictive wins, not last write. `Authorize` records the responsible rule’s reference ID and updates outcome, outcome name, priority and until timestamp as applicable. On equal outcomes its prose compares prioritization; on equal outcome and priority it says a nearer future retention replaces the existing timestamp. `Authorize_Until` instead describes retaining the greatest date and then greatest time on a tie. Do not silently merge these into one algorithm. The source also confuses “outcome prioritization code” with numeric negative outcomes when describing fired-value behavior; p87’s Fire section is clearer about outcome values. [pp77–80,87]

`Authorize`, `Approve` and `Decline` describe optional date/time as an **absolute SAS date plus time of day**. `_Until` methods describe **days and seconds added to `message.sas.system.messageDtTmUtc`**, returned in `message.sas.decision.outcomeUntilDtTime`. However, non-Until Exclude/Pickup/Refer/Verify sections themselves describe **relative** days/seconds, despite similar signatures. This is a source inconsistency, not a license to infer identical behavior from names. Do not execute retention-bearing calls for an unresolved variant until release-matched documentation/support or authorized isolated observed evidence resolves it. Non-retention authoring can continue. [pp75–98]

The Approve_Until example describes 12 hours but supplies 12 seconds; the Authorize_Until example describes 7 hours but supplies 7 seconds. The Refer_Until example uses 21,600 seconds for six hours, and Verify_Until describes 12 seconds. Preserve documented units and independently calculate expected timestamps; never copy the discrepant examples to resolve the conflict. Retention is a **recommendation returned to the authorization system**, not proof that a remote entity is actually exempted or that the engine will persist/reapply it to later messages. [pp76–81,94–99]

For authorized isolated acceptance, test a stronger prior outcome, weaker subsequent outcome, equal outcome/different approved priority, equal outcome/equal priority/different timestamp, omitted optional arguments, and relative day/second boundaries against known message UTC time. Retain return status, resulting outcome/name/priority/until/reference fields and downstream response separately. No observed results were obtained in this review.

## 3. Track firing, model results and diagnostic output

| Method | Contract and required check | Source |
| --- | --- | --- |
| `detection.Fire()` | Marks current variable or decision rule fired; no decision/alert follows merely from this marker. Alert, Authorize with outcomes 20/30/40/50, Decline, Pickup, Refer and Verify are explicitly listed as marking fired. Do not infer every Until variant or Approve/Exclude does so without target evidence. | pp87–88 |
| `detection.hasRuleFired('RuleID')` | Returns 1/0 for a previously executed rule; quoted base rule ID **without version**, same project. Candidate choices refresh on save and depend on project, message scope and dependencies. Creates a strong dependency; profile-value sharing instead creates weak dependencies, potentially across projects. | pp30–31,88–89 |
| `detection.Score('model name')` | Retrieves existing named registered-model score, described under `message.sas.modelfired[].score`; not an invocation of a scoring service. Missing-model return behavior is unspecified. | pp95–96 |
| `detection.scoreIndex('model name')` | Returns registered model-result array index. This section calls the array `message.modelFired`, unlike Score; the example also has an unmatched parenthesis. Resolve actual schema spelling, index validity and bounds before direct field/supplemental-score access. Missing-model/index failure behavior is unspecified. | pp96–97 |
| `detection.Debug('message')` | Debug text is conditional: all-message verbosity uses the documented CLI verbose control above zero, or one-message `request.logLevel` above zero. During test/impact analysis, log entries include line, timestamp, analysis phase, rule ID and text. Documented cap is 256 debug messages; an overflow note is emitted. Avoid customer payloads/secrets in debug strings. | pp81–82 |

When configuring engine logging or inspecting runtime diagnostics, read [engine-runtime.md](engine-runtime.md). When changing CLI verbosity or invoking the older command surface, read [detection-cli.md](detection-cli.md) and match installed help; this method reference does not grant runtime configuration authority.

## 4. Consume lists and arrays safely

`lists.<actual-list-name>.contains(key1,…,keyN)` returns 1 for a matching full key and 0 for no matching full key. `lists.<actual-list-name>.lookup(key1,…,keyN)` returns 1 with values available under `listValues.<actual-list-name>.<actual-column>`, or 0 for no match. Pass **every key**, with actual editor-displayed order/types. Read values only after success; no trustworthy miss payload, duplicate-key resolution or special transport-error return is documented. List-service failure remains distinct from a legitimate 0. For publication, expiry, associations and visibility, load [advanced-lists.md](advanced-lists.md). [pp69–70,73–74,99–100]

Local arrays exist only during the declaring rule and are cleaned up afterward; they are not persisted profiles. Scalar arrays are fixed-length homogeneous schema variables. Package/variable-set arrays are schema sets of type **Array of objects** and can contain mixed field types. Create the correct schema shape before using indexed member paths; a scalar’s `array[index]` is not an object array’s `array[index].field`. [pp27–29]

Use actual lower/upper bounds; `LBOUND(array)` and `HBOUND(array)` accept only the required array argument, as does `DIM(array)`. Use these only on arrays. The local-array example starts one loop at 0 despite its declaration not documenting a zero lower bound; another until-loop’s inner `k=10` branch is unreachable within its stated `k>6` termination condition. These are review hazards, not a recommended indexing/loop recipe. Verify empty/padded elements, first/last valid index and termination for the actual declared/schema shape; do not assume example paths or loop behavior are portable. [pp27–29,103–105]

## 5. Select from the supported function families

This is a factual identifier inventory grouped for task selection, not a general DS2 tutorial or permission to call any function in a similarly named family. Appendix 2 provides names and a few restrictions, **not full argument/return/error semantics for every function**. For a chosen function obtain its release-matched DS2 contract, then apply the Detection restrictions and editor validation. Missing general syntax does not justify guessing it. [pp101–108]

| Task family | Explicit supported identifiers |
| --- | --- |
| Character-class presence | `ANYALNUM`, `ANYALPHA`, `ANYCNTRL`, `ANYDIGIT`, `ANYFIRST`, `ANYGRAPH`, `ANYLOWER`, `ANYNAME`, `ANYPRINT`, `ANYPUNCT`, `ANYSPACE`, `ANYUPPER`, `ANYXDIGIT` |
| Character-class absence | `NOTALNUM`, `NOTALPHA`, `NOTCNTRL`, `NOTDIGIT`, `NOTFIRST`, `NOTGRAPH`, `NOTLOWER`, `NOTNAME`, `NOTPRINT`, `NOTPUNCT`, `NOTSPACE`, `NOTUPPER`, `NOTXDIGIT` |
| String assembly/normalization | `CAT`, `CATS`, `CATT`, `CATX`, `COMPBL`, `COMPRESS`, `DEQUOTE`, `LEFT`, `LOWCASE`, `PROPCASE`, `QUOTE`, `REPEAT`, `REVERSE`, `RIGHT`, `STRIP`, `TRANSLATE`, `TRANWRD`, `TRIM`, `TRIMN`, `UPCASE` |
| String comparisons/search/slices | `COMPARE`, `COMPFUZZ`, `COMPGED`, `COMPLEV`, `COUNT`, `COUNTC`, `COUNTW`, `FIND`, `FINDC`, `FINDW`, `INDEX`, `INDEXC`, `INDEXW`, `LENGTH`, `LENGTHC`, `LENGTHN`, `RANK`, `SCAN`, `SOUNDEX`, `SUBSTR`, `SUBSTRN`, `VERIFY` |
| Choices, missing values and conversion | `CHOOSEC`, `CHOOSEN`, `COALESCEC`, `COALESCE`, `IFC`, `IFN`, `MISSING`, `INPUTC`, `INPUTN`, `PUT` |
| Arrays | `DIM`, `HBOUND`, `LBOUND` |
| Bit operations | `BAND`, `BLSHIFT`, `BOR`, `BRSHIFT`, `BXOR` |
| Arithmetic, rounding and aggregation | `ABS`, `CEIL`, `CEILZ`, `COS`, `EXP`, `FLOOR`, `FLOORZ`, `FUZZ`, `INT`, `INTZ`, `LARGEST`, `LOG`, `MAX`, `MEAN`, `MIN`, `MOD`, `MODZ`, `RANGE`, `ROUND`, `ROUNDE`, `ROUNDZ`, `SIGN`, `SIN`, `SMALLEST`, `SQRT`, `STD`, `SUM`, `TRUNC` |
| Date/time construction, intervals and components | `DATDIF`, `DATE`, `DATEJUL`, `DATEPART`, `DATETIME`, `DAY`, `DHMS`, `HMS`, `HOUR`, `INTCK`, `INTNX`, `JULDATE`, `JULDATE7`, `MDY`, `MINUTE`, `MONTH`, `QTR`, `SECOND`, `TIME`, `TIMEPART`, `TODAY`, `WEEK`, `WEEKDAY`, `YEAR`, `YRDIF`, `YYQ` |
| Geography and postal/state conversion | `FIPNAME`, `FIPNAMEL`, `FIPSTATE`, `GEODIST`, `STFIPS`, `STNAME`, `STNAMEL`, `ZIPCITY`, `ZIPCITYDISTANCE`, `ZIPFIPS`, `ZIPNAME`, `ZIPNAMEL`, `ZIPSTATE` |

**Detection-specific restrictions:** `DIM`, `HBOUND`, `LBOUND` take only the array argument. `INTNX` allows the alignment optional argument, not multiple or shift-index. `SCAN` allows delimiters, not modifier. Printed p104 requires the `FINDW(string,word,chars,modifier(s)[,startpos])` form, but its extracted table detaches the remaining limitations from their function rows: forbidden delimiter, forbidden `w`, forbidden `w` and `d`, and forbidden multiple/`method`/shift-index. Until that physical table is inspected, do not assign those detached restrictions to INDEXW/INPUTC/INPUTN/INTCK by inference or execute an affected optional-argument call. The p105 LBOUND note mistakenly names HBOUND; use the row’s LBOUND restriction, preserving the typo as a source defect. [pp103–105,107]

**Finish:** selected supported identifier, verified DS2 signature/units/return/missing/error behavior, Detection-specific restrictions and expected boundary fixtures. Availability in a list does not demonstrate runtime precision, locale, calendar or resource behavior.

## 6. Select supported formatting families without assuming conversions

Appendix 2 pp109–111 is titled **Formats and Informats**, but its introductory sentence calls the entries formats. It does not separately establish input/output support or parameter ranges for every entry. Obtain the exact DS2 format/informat contract and supported direction before applying `INPUTC`, `INPUTN` or `PUT`; do not infer that an output format is automatically a valid input informat. `w`, `d`, `p` and `x` in the manual are syntax parameters, not literal field names.

| Purpose | Explicit listed identifiers / parameterized families |
| --- | --- |
| Character representations | `$BASE64Xw.`, `$BINARYw.`, `$HEXw.`, `$OCTALw.`, `$QUOTEw.`, `$REVERJw.`, `$REVERSw.`, `$UPCASEw.`, `$UUIDw.`, `$w.` |
| Binary/numeric representations | `BESTDOTXw.`, `BESTDw.p`, `BESTw.`, `BINARYw.`, `Dw.p`, `Ew.`, `FLOATw.d`, `FRACTw.`, `HEXw.`, `IEEEw.d`, `OCTALw.`, `ROMANw.`, `VAXRBw.d`, `w.d`, `Zw.d` |
| Numeric punctuation, money and percent | `COMMAw.d`, `COMMAXw.d`, `DOLLARw.d`, `DOLLARXw.d`, `EUROw.d`, `EUROXw.d`, `NEGPARENw.d`, `PERCENTw.d`, `PERCENTNw.d`, `YENw.d` |
| Basic ISO date/time | `B8601DAw.`, `B8601DNw.`, `B8601DTw.d`, `B8601DZw.`, `B8601LZw.`, `B8601TMw.d`, `B8601TZw.` |
| Extended ISO date/time | `E8601DAw.`, `E8601DNw.`, `E8601DTw.d`, `E8601DZw.`, `E8601LZw.`, `E8601TMw.d`, `E8601TZw.d` |
| Date/day/month/year and localized forms | `DATEw.`, `DAYw.`, `DDMMYYw.`, `DDMMYYxw.`, `DOWNAMEw.`, `JULIANw.`, `MMDDYYw.`, `MMDDYYxw.`, `MMYYw.`, `MMYYxw.`, `MONNAMEw.`, `MONTHw.`, `MONYYw.`, `NENGOw.`, `WEEKDATEw.`, `WEEKDATXw.`, `WEEKDAYw.`, `YEARw.`, `YYMMw.`, `YYMMxw.`, `YYMMDDxw.`, `YYMMDDw.`, `YYMONw.` |
| Timestamp-derived date forms | `DTDATEw.`, `DTMONYYw.`, `DTWKDATXw.`, `DTYEARw.`, `DTYYQCw.` |
| Clock and combined timestamp | `DATEAMPMw.d`, `DATETIMEw.d`, `HHMMw.d`, `HOURw.d`, `MDYAMPMw.d`, `MMSSw.d`, `TIMEAMPMw.d`, `TIMEw.d`, `TODw.d` |
| Quarter/year-quarter | `QTRRw.`, `QTRw.`, `YYQxw.`, `YYQRxw.`, `YYQZw.`, `YYQRw.`, `YYQw.` |

The source repeats `PERCENTw.d`; it is one identifier, not an extra feature. Encoding/display support does not grant authority to encode/export restricted data. Validate rounding, width overflow, missing values, leading-zero preservation, locale separators and timezone/UTC interpretation for the chosen conversion. No SAS conversions were executed during source review.

## 7. Source-example disposition and completion

The introductory card-rule example (p2), assistant screenshots (pp13–19), three array examples (pp27–29), list examples (pp69–70,74,100), and every method example (pp74–99) were reviewed as source text for intent and hazards, not copied or executed. Their schema paths, model labels, thresholds, account values, country codes and priorities are illustrative, not an installed asset contract or fraud policy. The p70 lookup example has unbalanced block structure; p74’s contains example has a stray end; p96–97’s scoreIndex example has malformed condition/block syntax; p99’s Verify_Until receiver is misspelled. The local-array and time-unit defects above remain explicit. No example is a verified executable template.

For assistant NOT AND, p15 prose says it is true only when all inputs are false, which is not equivalent to ordinary negation of AND. Do not substitute Boolean algebra for this ambiguous UI description: compare generated code and an explicit mixed-true/false truth table in the authorized target before relying on it. NOT OR is separately described as true when all are false. Screenshot content remains a visual-review gap in this pass.

Return the authored specification/code revision, actual supported signatures and data contracts, saved validation, expected cases, every unresolved source contradiction and observed versus unperformed results. An unresolved retention variant, optional-argument table cell or model-array contract blocks only that dependent executable call; complete the remaining offline specification without inventing it.
