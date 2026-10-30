# BOSS expression language, functions and interpolation

Load when authoring or diagnosing a BOSS mapping, query, template call or interpolated property. For action selection and connected-system effects, read [boss-actions.md](boss-actions.md); for project assembly, authorization, deployment and business acceptance, read [boss-orchestration.md](boss-orchestration.md). When checking whether a property supports an expression or whether the project shape is valid, read [boss-schema-contract.md](boss-schema-contract.md).

## Source and limits

Original functional synthesis of SAS Institute Inc., *SAS Business Orchestration Services: User’s Guide*, **2026.03**, May 5, 2026, `v_032-P3:eopug`, printed **225–392**, chapters 6–8. Local source: `.sources/archive/partially-reviewed/2026.03/SAS BOSS - User Guide.pdf`; recorded SHA-256 `115a7500a113769f6deb47ed1a068365e8e574edfab1625b717b5dcd9e3937b4`. All extracted text in those pages, including examples, was read; this is not execution evidence or an assertion that all physical-page visuals were inspected. The printed-p287 validation-result screenshot is absent from extraction and requires separate visual coverage. Source inconsistencies below are preserved as uncertainty rather than silently corrected into product guarantees. No upstream sample bodies are reproduced.

## Author a mapping contract

1. Name the source type, input paths, metadata and batch context; expected output type/paths; missing/null/empty policy; units; and error/drop behavior.
2. Select functions for independently obtained values and methods for transformation of a receiver. `receiver.method(...)` passes its receiver implicitly; chained methods consume the prior result. Use explicit `root` and `this` even though assignment contexts can infer them. Arguments can be positional or named. A query argument is an expression returning one value, often `item -> ...`; validate the required return type.
3. Choose projection or preservation deliberately. `this` is the current processor's input; `root` is the output being constructed. Unassigned root leaves input unchanged; once constructing output, unmapped input fields are not automatically retained. Start with `root = this` only when retention is intended. Each processor's output becomes the next processor's input, not a shared mutable source object.
4. Plan expected versus observed cases before any authorized runtime test: correct type, wrong type, missing/null, empty collection, boundaries, malformed parsing/encoding, metadata changes, batch index/size and error paths. Unknown target cadence or undocumented edge behavior blocks a compatibility claim, not a complete offline candidate.
5. Preserve diagnostics on failure and route deliberately. A method fallback is not permission to manufacture valid business data. Dropping is not a delivered rejection, a fraud decision or external rollback. No SAS execution, installation, authentication or connectivity is implied by this reference.

### Syntax and contexts (pp225–246)

- Supported literals: arrays, objects, Boolean, number, string (including multiline), null. `#` starts a line comment. Assignments use dot-separated paths. Quote a path segment containing spaces or literal dots; distinguish a literal dotted key from a nested object.
- `let name = ...` defines a local value, referenced as `$name`. Named `map name { ... }` mappings are applied with `.apply("name")`; `import` can load mapping files. Supply all required map inputs in the receiver object. Source examples are not a general lexical-scope contract.
- Arithmetic `+ - * / %` requires numbers; `+` also concatenates two strings. `!` requires Boolean. Equality requires matching basic type and value; numeric ordering requires numbers; `&&` and `||` require Booleans. Wrong types produce recoverable mapping errors, not implicit JavaScript-style coercion.
- `|` selects the first existing non-null candidate; parenthesized path alternatives allow optional paths. Within a nested path context, `this` refers to that nested value. Preserve an explicit final alternative when business policy permits it.
- `if`/`else` returns conditional values. `match` evaluates alternatives with `_` as the unmatched/default case. Order and fallback belong in the expected-case table.
- `meta key = ...` writes metadata; `meta = deleted()` deletes metadata; `@key`/`@` accesses metadata. The chapter-7 `metadata()` contract explicitly reads immutable input metadata, whereas `@.key` can observe mutations within the mapping. Do not substitute them when order matters.
- Assigning `deleted()` to a field removes that field; assigning it to root drops the whole message. `content()` accesses raw/unstructured bytes; `this` is not the raw-text accessor.

## Encoding methods (pp251–257)

| Method | Selection, inputs and result | Constraints / error contract |
| --- | --- | --- |
| `compress(algorithm?, level?)` | Compress string/bytes. Algorithms: `flate`, `gzip`, `pgzip`, `lz4`, `snappy`, `zlib`, `zstd`; level default -1. | Level applicability varies by algorithm. The source labels algorithm optional without establishing a portable default: specify it. Treat output as binary, not plain JSON text. |
| `decompress(algorithm)` | Decompress string/bytes using `bzip2`, `flate`, `gzip`, `pgzip`, `lz4`, `snappy`, `zlib`, `zstd`. | Match actual upstream compression; do not assume compression and decompression have identical algorithm sets. |
| `encode(scheme)` | String/bytes → encoded string. | Schemes: `base64`, padded `base64url`, unpadded `base64rawurl`, `hex`, `ascii85`. Encoding is not encryption. |
| `decode(scheme)` | Encoded string → bytes, same scheme choices. | In a JSON field, explicitly `.string()` for text or `.encode(...)` for an intended encoding; otherwise byte values serialize as Base64. Malformed input needs an error case. |
| `decode_sas_uri()` | SAS tilde-encoded URI segment → decoded string. | For SAS reserved-segment encoding, not a replacement for ordinary URL query decoding. |

## General functions and methods (pp257–273)

| Identifier | Functional / selection contract | State, failure or edge contract |
| --- | --- | --- |
| `.apply(mapping)` | Apply named mapping to receiver; null receiver can construct independent data. | Bundle multiple inputs into one receiver object; name/import must exist in target mapping environment. |
| `.catch(fallback)` | Evaluate fallback only for target-query failure; lambda can receive the error. | Null success is not itself failure. Distinct from the action-level `catch` processor. |
| `counter(min?, max?, set?)` | Independent incrementing counter per instantiation; first value min (default1), max default9223372036854775807; wraps after max. | Dynamic min/max resolve once per mapping lifetime. `set` evaluated each call: nonnegative integer sets/yields it; omitted/no result increments; null holds; deletion resets to min. Not a durable global sequence. |
| `deleted()` | Deletion marker for mapped field/item/message. | A dropped message is acknowledged as successfully processed by the flow; not an error response. |
| `.exists(path)` | Test object-relative JSON dot path presence → Boolean. | Presence and non-null/non-empty business validity are separate tests. |
| `.from(index)` | Evaluate supported `content`, `json`, `metadata` query from another batch message. | Index is batch-relative; do not assume changing arbitrary method receivers changes message perspective. |
| `.from_all()` | Evaluate those supported queries for all batch messages → array. | Establish actual batching policy; aggregate message perspective is not external persistent state. |
| `ksuid()` | New K-sortable identifier string per invocation. | Inbound metadata already includes `messageId` and `batchId`; regenerating an ID does not preserve correlation. |
| `nanoid(length?, alphabet?)` | New NanoID string, optionally chosen length/alphabet. | Custom alphabet requires explicit length; no source-backed business uniqueness guarantee. |
| `.or(fallback)` | Fallback for failure **or null**; explicit alternative to coalescing pipe. | Do not confuse with `.catch`, which handles failure only. |
| `random_int(seed?, min?, max?)` | Nonnegative pseudorandom 64-bit integer; defaults min0, max9223372036854775806. | Seed query resolves once per mapping lifetime; min/max cannot use dynamic mapped expressions. Not a security token generator. Source gives modulo as an alternative for dynamic bounds, not a uniformity/security guarantee. |
| `range(start, stop, step?)` | Integer array, start inclusive / stop exclusive; step defaults1. | Negative step is supported when stop < start. Zero-step and inconsistent-direction behavior are not specified here: reject in candidate inputs or obtain target evidence. |
| `throw(why)` | Produce mapping error with explanatory string, abandoning the mapping. | Preserve and route error; this is not flow termination or rollback of prior effects. |
| `ulid(encoding?, random_source?)` | ULID string; `crockford` default or `hex`; `secure_random` default or `fast_random`. | Keep secure source for security-sensitive uses; source does not make an identifier an authorization secret. |
| `uuid_v4()` | New RFC4122 UUID string each invocation. | Keep generation separate from source transaction identity and idempotency. |

## Environment and message functions (pp273–291)

| Identifier | Contract | Limits |
| --- | --- | --- |
| `env(name, no_cache?)` | Environment value; missing → null. | Static name caches first resolution; `no_cache:true` rereads on each mapping invocation. |
| `file(path, no_cache?)` | Read file contents; relative path from executing process directory. | Static path caches first read; true rereads. Filesystem access/mounts/permissions must actually exist; never read secrets into output. |
| `file_rel(path, no_cache?)` | Same, relative to mapping directory. | Do not confuse process-relative and mapping-relative paths. |
| `now()` | Current RFC3339 string in local timezone. | Not the input's event timestamp; local zone matters. |
| `timestamp_unix()` / `timestamp_unix_milli()` / `timestamp_unix_nano()` | Current Unix-epoch count in seconds / milliseconds / nanoseconds. | Retain unit and precision; do not mix with duration or source event time. |
| `batch_index()` / `batch_size()` | Current message's zero-based index / batch message count. | One-message batches are possible; index-based filtering changes delivery. |
| `content()` | Full raw source message bytes. | JSON embedding defaults to Base64 unless explicitly cast/encoded. |
| `json(path?)` | Source JSON dot-path value, or whole source document when omitted. | Targets whole source document regardless of nested mapping context; malformed structured input needs error handling. |
| `metadata(key?)` | Read-only input metadata key (missing → null), or whole metadata object. | Does not reflect current-map mutations; use `@.key` for that. Supports batch perspective methods. |
| `error()` / `errored()` | Cause string or null / Boolean error flag. | Preserve diagnostics before action-level catch clears error state; an HTTP status requires explicit response configuration. |
| `template(template_file_name, variables_map)` | Render configured external template with supplied variable values. | Detailed contract below; failure can stop project/pod, not merely produce a harmless blank string. |

### Content template contract

Templates are preloaded from `workloads[].configurations.contentTemplates.templatePath`. At call time provide only a file name: absolute paths and `../` are prohibited. Supported extensions: `.html` uses Go `html/template` with autoescaping; `.json`, `.txt`, `.xml`, `.yaml`, `.yml` use Go `text/template`. `$variable` and `${variable}` are documented placeholders; do not assume the two rendering engines provide identical output escaping.

Supply named, sanitized nonempty values and every referenced variable; avoid embedded logic and HTML unless needed. Documented errors include `invalid_format` (unsupported format; project stops), `unresolved_variable`, `empty_variable`, `invalid_email_format` for a variable named `email`, `security_violation_control_character_detected` (template not loaded/pod stops), `security_violation_illegal_html_tag_detected`, `security_violation_illegal_uri_detected`, and `security_violation_inline_event_handler_detected`. Blocked tags include script, iframe, object, embed, svg, link, style; blocked schemes include javascript, data:text/html and data:image/svg+xml. Encoded inputs are decoded/revalidated against script injection. Structured parameter errors use variable-name plus reason. These are documented protections, not proof arbitrary content is safe or that mail was delivered.

`validateWithMessageSchema` can populate `message_validation` metadata with validation failures; the source illustrates separating errors into an HTTP response body. Use actual metadata/error behavior of the target edition and explicit response status, rather than treating a log or a populated error field as rejection delivered to the caller.

## Numeric methods (pp291–307)

| Methods | Result and selection | Constraints |
| --- | --- | --- |
| `abs()` | Absolute int64/float64; minimum integer specially becomes maximum integer. | Do not assume mathematically lossless absolute value at integer minimum. |
| `ceil()` / `floor()` / `round()` | Ceiling / floor / nearest integer, with halves away from zero for round. | Return int64 if fitting, otherwise floating point. Define rounding before integer casts. |
| `float32()` / `float64()` | Explicit native-width float conversion; string parsing attempted. | Precision differs; malformed/out-of-range conversion needs target error cases. Manual refers externally to ParseFloat; full accepted grammar not established locally. |
| `int8()` / `int16()` / `int32()` / `int64()` | Explicit signed-width conversion from number or parseable string. | Capacity overflow or fractional value errors; round first only when policy permits. Manual refers to ParseInt; do not invent additional accepted forms. |
| `uint8()` / `uint16()` / `uint32()` / `uint64()` | Explicit unsigned-width conversion. | Negative, fractional or overflow values require failure cases. Prose erroneously calls string parsing signed; method identity/examples are unsigned, so establish runtime parsing before relying on boundaries. |
| `log()` / `log10()` | Natural / decimal logarithm. | Domain-error and nonfinite serialization behavior not specified in these entries; require positive finite inputs or obtain target evidence. |
| `max()` / `min()` | Largest / smallest value of numeric array. | Empty or any nonnumeric element errors. |

## Object and array methods (pp307–342)

| Method | Input → output / use | Edge and error contract |
| --- | --- | --- |
| `all(test)` / `any(test)` | Array → Boolean for all/any query results. | Both return false on empty array; non-array or non-Boolean predicate errors. Do not import vacuous-truth semantics. |
| `append(values...)` | Append elements to receiver array. | Unlike concat, each supplied value is an element. |
| `concat(arrays...)` | Concatenate receiver and argument arrays. | Preserve actual element types; not string concatenation. |
| `assign(with)` | Merge source into destination; source overwrites non-object key collisions. | Choose deliberately instead of merge when scalar type must remain scalar. |
| `merge(with)` / `squash()` | Merge objects / condense array of objects; collisions retain both values in arrays; existing arrays expand. | Output shape can change on collision; validate downstream schema. |
| `collapse(include_empty?)` | Array/object → flat dot-path-key object. | Empty objects/arrays excluded unless include_empty true. Literal dots can be ambiguous downstream. |
| `contains(value)` | Array element or object **value** membership → Boolean. | Numeric comparisons ignore native numeric width; not object-key existence. |
| `enumerated()` | Array → objects with `index` and `value`. | Index is zero-based. |
| `explode(path)` | Array at path → array of source-shaped objects with one target element each; object at path → keyed object of source-shaped values. | Other source fields are retained in each result. Printed syntax omits argument but all examples supply path: verify exact target signature. Does not itself prove flow-level message fan-out. |
| `filter(test)` | Filter array elements / object key-value pairs. | Keeps only Boolean true; false **and non-Boolean** remove item (unlike all/any errors). |
| `flatten()` | Nested arrays → one-dimensional array. | For flattening arrays, not flattening object paths (collapse). |
| `fold(initial, query)` | Array → accumulated tally of chosen type. | Query receives `tally` and `value`, returns next tally; initial may be number/string/object. Not limited to addition despite introductory wording. |
| `get(path)` | Object-relative dynamic dot-path lookup. | Distinct from `json()` whole-source perspective. |
| `index(index)` | Array element by zero-based index; negatives count from end, -1 last. | Byte-array element is integer per index entry; another bytes example prints a quoted integer. Resolve output-type inconsistency with target evidence. |
| `join(delimiter?)` | Array of strings → string. | Convert numeric items explicitly first. Manual misspells named parameter as `delimeter`; use positional form or verify target name. |
| `keys()` / `values()` / `length()` | Object keys array / values array / array length or object key count. | Values order is explicitly random; do not depend on incidental key/value ordering. |
| `map_each(query)` | Transform array elements or object values; deletion can omit items. | Object example exposes `value` on key-value context; do not assume identical receiver context for objects and arrays. |
| `map_each_key(query)` | Object → object with transformed keys. | Result must be string; define collision expectations before renaming keys. |
| `slice(low, high?)` | Array half-open slice; high defaults length; negative indexes are end-relative. | Low beyond sequence length returns empty; other invalid-range behavior is not fully specified. |
| `sort(compare?)` | Increasing-order homogeneous strings/numbers; optional comparator permits other structures. | Comparator context has `left`,`right`; return Boolean left-less-than-right. Mixed default types fail. |
| `sort_by(query?)` | Increasing-order sort by per-element query key. | Comparable key types must match; do not mistake lexical numeric strings for numbers. |
| `sum()` | Numeric array → sum. | Do not use the printed example's inconsistent total as an oracle; plan own independently calculated cases. |
| `unique(emit?)` | Remove duplicate elements; optional query supplies uniqueness key. | Number5 and string"5" remain distinct. |
| `with(paths...)` / `without(paths...)` | Retain only paths / remove paths. | Missing nested keys ignored; without does not remove nonexistent/non-object nested path. |
| `zip(arrays...)` | Equal-length arrays → aligned tuple arrays. | Lengths must match; source output renders numeric tuple items as strings without a conversion rule. Do not assume implicit string coercion. |

## Parsing and serialization methods (pp342–353)

| Method | Contract | Important distinctions |
| --- | --- | --- |
| `format_xml(indent?, no_indent?)` | Structured receiver → XML bytes; indent defaults one space, no_indent false. | Use `.string()` to embed XML text in JSON; no_indent true disables line indentation. |
| `format_yaml()` | Structured receiver → YAML bytes. | Printed signature says `format()` but heading/examples say format_yaml: do not replace with string-format method. |
| `parse_csv(parse_header_row?, delimiter?, lazy_quotes?)` | RFC4180 text → objects keyed by header (default true), or row arrays. | Delimiter one character, comma default; lazy_quotes false default. Entry's table misspells delimiter, while example uses `delimiter`; prefer verified/positional syntax. Not the scanner's default contract. |
| `parse_form_url_encoded()` | Form-urlencoded query/body string → structured result. | It is distinct from CSV despite misplaced CSV examples under its heading. Repeated-key shape is not specified in these pages. |
| `parse_json(use_number?)` | JSON string → parsed value; optional json.Number rather than float64 parsing. | Source example uses a quoted number and does not demonstrate preservation of an unquoted large JSON integer: test actual precision and type. |
| `parse_url()` | URL string → structured facets, e.g. scheme, host, path, fragment, raw query and optional user. | Syntactic parsing is not trusted-destination validation or connectivity. |
| `parse_xml(cast?)` | XML string → object, default strings. | Attributes prefixed `-`; attributed simple text uses `#text`; repeated elements become arrays; comments/directives/processing instructions ignored. Cast true attempts number/Boolean conversion. |
| `parse_yaml()` | Single YAML document string → structured value. | Not a multiple-document stream parser. |

Parsing, decompression, decoding and casts require explicit malformed-input cases. Where the entry does not name an exact failure message or behavior, report it unknown until observed in the selected release; do not substitute a different library's behavior.

## Regular-expression methods (pp353–360)

All take a string `pattern`; establish escaping in both YAML and expression strings. The manual's named-group examples are not a contract for every Python regex feature.

| Method | Result |
| --- | --- |
| `re_find_all(pattern)` | Array of successive whole-match strings. |
| `re_find_all_object(pattern)` | Array of match objects; whole match key `0`, subgroups by explicit name or numeric group index. |
| `re_find_all_submatch(pattern)` | Array of arrays: whole match followed by captured subexpressions. |
| `re_find_object(pattern)` | First match object, same key convention. |
| `re_match(pattern)` | Boolean for a match anywhere; anchor explicitly for whole-string validation. |
| `re_replace_all(pattern, value)` | Replace all matches; `$1` etc. expand submatches in replacement. Escape literal dollar intent rather than assuming literal replacement. |

No-match shape for each extraction method and invalid-pattern diagnostics are not fully specified in these entries; include both in target acceptance rather than asserting null/empty interchangeability.

## String methods (pp360–379)

| Methods | Use and output contract |
| --- | --- |
| `capitalize()` / `lowercase()` / `uppercase()` | Word-initial Unicode title casing / lowercase / uppercase string. Not a locale-specific identity-normalization policy. |
| `contains(value)` / `has_prefix(value)` / `has_suffix(value)` | Substring / prefix / suffix Boolean. Use string arguments; numeric-argument example is not a general coercion rule. |
| `escape_html()` / `unescape_html()` | Escape `< > & ' "` / decode broader HTML entities. Not a general sanitizer. |
| `escape_url_query()` / `unescape_url_query()` | URL-query escaping / unescaping; source examples show percent encoding and plus-as-space decoding. Prose incorrectly repeats HTML-entity language; do not infer HTML semantics or use for URI path segments. |
| `filepath_join()` / `filepath_split()` | Array of path elements → OS-dependent path / path → `[directory,filename]`; absent separator gives empty directory. Not path-traversal authorization. |
| `format(args...)` | Receiver format string → formatted string with arguments (source refers to Go fmt verbs). Full external verb catalog not reproduced or assumed reviewed. |
| `index_of(value)` | First substring start index, -1 if absent. |
| `length()` | String length; source does not establish Unicode byte/codepoint/grapheme units. Include non-ASCII case when indexes/length matter. |
| `quote()` / `unquote()` | Add escaped quotation representation / expand escaped quoted representation, including control/nonprintable escapes. Malformed quoting needs a failure case. |
| `replace_all(old,new)` | Literal replacement of every occurrence; no regex capture expansion. |
| `replace_all_many(values)` | Array of alternating old/new strings, pairs applied as documented compact replacement operation. Supply complete string pairs; do not infer odd-length behavior. |
| `reverse()` | Reverse string; non-ASCII ordering granularity not specified. |
| `slice(low,high?)` | Half-open string range; high defaults length, negative indexes end-relative; low beyond length gives empty. Non-ASCII indexing units require target evidence. |
| `slug(lang?)` | Generate slug, optionally language-dependent. External language/transliteration catalog is not established by these pages. |
| `split(delimiter)` | String → array split on string separator. Distinct from regex matching. |
| `strip_html(preserve?)` | Remove tags, optionally retain array of element types. Retained HTML is not safe by construction. |
| `trim(cutset?)` | Remove leading/trailing characters in cutset; omitted means whitespace. Not substring trimming. |
| `trim_prefix(prefix)` / `trim_suffix(suffix)` | Remove matching boundary string; otherwise unchanged. |

## Duration and type checks (pp379–388)

- `parse_duration()` turns signed decimal/unit sequences into integer nanoseconds. Units: `ns`, `us`/`µs`, `ms`, `s`, `m`, `h`; fractions and combined units supported. Convert units explicitly; not a calendar-month/year parser.
- `bool(default?)` parses values; nonzero number true. Documented string forms include `1,t,T,TRUE,true,True,0,f,F,FALSE`; other forms are not established by this local list. Optional Boolean default handles parse failure. No default means failure must remain visible.
- `bytes()` converts to bytes (existing bytes unchanged); JSON field representation is Base64. Binary methods do not require that every input message be JSON: the source's blanket bytes-note wording must be read alongside its documented raw-content support.
- `not_empty()` returns nonempty string/array/object or errors; `not_null()` returns non-null value or errors, so empty object can pass not_null. Neither is a Boolean predicate.
- `number(default?)` parses to number or optional float fallback; `string()` marshals to string, unchanged if already string. Neither validates business scale/units.
- `type()` returns `string`, `bytes`, `number`, `bool`, `array`, `object`, or `null`; these basic categories are not all native numeric widths.

## Interpolation and evaluation time (pp389–392)

Use interpolation only on a documented supporting property. Environment `${NAME}` is resolved when service starts/processes the project; missing variable without default prevents startup. `${NAME:default}` supplies a default; `${NAME:}` explicitly permits empty. `${{NAME}}` produces a literal `${NAME}`. Keep secrets in approved injected environment values, not project text.

`${! expression }` evaluates per message at runtime; it is the query/right-hand-side expression surface, not an arbitrary mapping statement block. Use `json()` for source fields and `metadata()` for input metadata. Startup interpolation, cached `env()` lookups and per-message expressions have different refresh timing.

The chapter's HTTP example resolves to a host inconsistent with its supplied input, and its sleep example uses `meta(...)` whereas the function catalog documents `metadata(...)`. Likewise schema defaults use `count(...)` not documented as a chapter-7 function. Do not invent aliases from sample text. Prefer catalog-backed names and establish any disputed syntax against the release-matched target contract before dependent live use.
