# AnyDoc Plan v4 — local plan contract

This reference defines the JSON accepted by `plan validate`, `plan show`, and
`plan approve` for a version 4 plan. The four authority kinds are deliberately
different. A plan is local execution data: it does not save a model, upload local
facts, or replace the human's choice of authority. Unknown properties are
rejected by the typed plan schema.

The complete executable examples below use synthetic data only. Their shared
source fixture is one local text file named `source.txt`; each example copies it
to `Source.txt`.

## Root cause and gap inventory

The concrete pre-fix local reproducer was a `manual_private_list` plan whose
embedded `authority.list` omitted `contract`. The typed draft requires that
field, so local plan loading failed with `invalid_plan` before validating its
actions. The contract belongs inside `authority.list` or `authority.model`, not
at the plan root. The local plan loader now derives the literal
`anydoc-assembly-model-v2` only when that embedded field is omitted; an explicit
wrong value remains invalid. `models validate` / `models save` / `models replace`
are separate draft APIs and still require the field explicitly. The plan digest
is calculated from the original JSON, before this local decode default, so the
omitted field is not silently added to the approval hash.

The documentation gap was that the authority kinds were described in workflow
prose without a single field-by-field contract for nested model/list data,
Public Guide evaluation integrity, exclusions, output limits, and actions. Two
common schema omissions are also easy to miss: `excluded_sources` is a required
array, and a plan must explicitly set `constraints.max_file_bytes` to a cited
integer or `null` (no cited cap). No other contract fields are supplied by the
local embedded-contract default.

An `invalid_plan` diagnostic names JSON field paths and expected structure,
not supplied case values, source filenames, person names, or model hash values.
Unknown property names are shown only as escaped JSON field paths.

Telemetry was not changed. `plan validate` emits the existing `plan_prepared`
signal and `plan approve` emits `plan_approved`; `plan show` currently has no
signal. Existing event properties are `outcome` and optional `duration_bucket`,
with the CLI error `code` carried separately as `error_code`; error text is not
sent. Use the local CLI error for diagnosis, not telemetry.
The reporting path is `src/cli.rs::signal` →
`src/signals.rs::emit_for_exit` → `emit_named`; assembly failures use
`src/assemble.rs::assemble` → `src/signals.rs::emit_assembly_completed` →
`emit_named`. A future redacted-message change belongs at this boundary.

## Plan v4 contract

### Root fields

| Field | Presence | Shape and rules |
|---|---|---|
| `version` | Required | Integer exactly `4`. This reference does not describe historical v3 plans. |
| `authority` | Required | Tagged object with exactly one `kind`: `manual_plan`, `manual_private_list`, `public_guide`, or `private_model`. See [Authority shapes](#authority-shapes). |
| `input_root` | Required | Relative directory used as the source root; `.` is valid. It must stay inside the selected input directory. |
| `output_dir` | Required | Intended new immutable version directory exactly `anydoc-output/vNNN`, where `NNN` is three digits and is not `000` (for example `anydoc-output/v001`). Assembly refuses to overwrite an existing snapshot. |
| `actions` | Required | Array of strict action objects. Each source path is relative to `input_root`; each `to` is a relative path inside `output_dir`. See [Action shapes](#action-shapes). |
| `excluded_sources` | Required | Array of source-exclusion objects; use `[]` when nothing is excluded. See [Source accounting](#source-accounting-and-exclusions). |
| `constraints` | Optional syntactically; required semantically | If present, it is an object with required `max_file_bytes`. Omitting the key or omitting `max_file_bytes` is refused. |
| `public_guide` | Conditional | Required for `authority.kind = "public_guide"`; optional for a matching private-model comparison or unsaved-list comparison. Omit it for `manual_plan` and when no guide was evaluated. |
| `official_case_specific` | Optional | Plan-local pointer and raw-file digest; requires version 4. It is not an authority or Public Guide fact. See below. |

The plan root has no `contract` field. `authority`, its nested typed objects,
and action objects reject unknown keys.

#### `constraints`

| Field | Presence | Shape and rules |
|---|---|---|
| `constraints.max_file_bytes` | Required | Unsigned integer: a cited byte cap from the receiving portal/guide; or explicit `null` to record that no cap is cited. AnyDoc does not invent a cap. With `null`, no output-size limit is checked. |

This cap is distinct from `compress_to_explicit_budget.max_bytes`, the target
for one explicit compression action. An output over a cited cap is refused
unless an explicit compression action is present; compression still respects
its selected clarity floor.

#### `official_case_specific`

| Field | Presence | Shape and rules |
|---|---|---|
| `reference` | Required | `local-case:<relative-path>` to a regular file inside the input directory; no traversal, absolute path, or symlink escape. The complete string is at most 512 bytes. |
| `sha256` | Required | Lowercase 64-character SHA-256 hex of the referenced file's bytes. |

AnyDoc checks the reference and digest during validate, approve, assemble, and
verify. This field remains outside `authority` and the Public Guide evaluation.

### Authority shapes

| `authority.kind` | Required fields besides `kind` | Meaning |
|---|---|---|
| `manual_plan` | None | The user directly supplied or approved the complete output actions. No embedded model contract. |
| `manual_private_list` | `list`, `list_sha256` | A user-provided private list kept locally as a typed `AssemblyModelDraft`; it is not saved to accountd. |
| `public_guide` | None in `authority`; requires top-level `public_guide` context | Public-only authority after explicit human opt-in. The context mode must be `public_only_confirmed`; comparison alone never selects this authority. |
| `private_model` | `model`, `people`, `condition_results`, `scope`, `action_mappings` | A complete saved private model plus this case's local role bindings, condition answers, selected scope, and exact source-to-claim mappings. |

Each tagged authority rejects fields belonging to another kind. The nested
shapes are below.

### Embedded model and private-list shapes

`authority.model` is a complete saved `AssemblyModel`. It is not an export
stripped of its identity fields. `authority.list` is a draft-shaped
`AssemblyModelDraft`; it must not contain server-owned model identity fields.

| Field | `authority.model` (saved model) | `authority.list` (private list draft) |
|---|---|---|
| `model_id` | Required; UUID | Not allowed |
| `contract` | Required in the typed model; exact literal `anydoc-assembly-model-v2` | Required in the typed draft; exact literal `anydoc-assembly-model-v2` |
| `case_type` | Required; `^[a-z0-9][a-z0-9._-]{0,127}$` | Same |
| `title` | Required; 1–120 characters | Same |
| `revision` | Required; positive integer | Not allowed |
| `roles` | Required; 1–32 role definitions | Same |
| `conditions` | Required; array, possibly empty | Same |
| `deliverables` | Required; 1–200 deliverables | Same |
| `model_hash` | Required; lowercase 64-character SHA-256 JCS digest described below | Not allowed |
| `list_sha256` | Not part of the model | Required beside `list`; domain-separated digest of the complete `list` draft described below |

**Local contract default.** In a Plan v4 only, the loader supplies
`"contract": "anydoc-assembly-model-v2"` when that one field is absent from
`authority.model` or `authority.list`. It rejects any explicit different value.
This does not make `contract` optional in the models draft API: standalone model
drafts passed to `models validate`, `models save`, or `models replace` must
include it. Never place it at the plan root.

#### Model/list nested fields

The same draft definition is used by the saved model and local private list;
`authority.model` additionally carries `model_id`, `revision`, and `model_hash`.

| Object / field | Required shape and validation |
|---|---|
| `roles[]` | Each role has `role_id`, `label`, `cardinality`. `role_id` is a unique lowercase identifier matching `^[a-z][a-z0-9_]{0,63}$`; `label` is 1–100 characters; `cardinality` is `one` or `many`. There must be 1–32 roles. |
| `conditions[]` | Each condition has `condition_id`, `label`, `scope`. Its unique `condition_id` uses the role-ID format; `label` is 1–200 characters; `scope` is `case` or `subject`. The array may be empty. |
| `deliverables[]` | 1–200 entries; all listed fields are required. `deliverable_id` uses the role-ID format and is unique. `sequence` is unique and matches 1–3 digits with an optional final uppercase letter (`^[0-9]{1,3}[A-Z]?$`). `filename_template` must render to a safe complete base filename, at most 180 characters and 240 bytes. `output_mode` is `single` or `per_person`; `serves_roles` is a non-empty list of unique declared role IDs; `when` is a condition object below; `contents` is non-empty. |
| `deliverables[].filename_template` | `per_person` requires exactly one `{person_name}`; `single` forbids it. No other brace placeholder is accepted. The rendered name must have a non-empty stem and extension and use portable filename characters. |
| `deliverables[].contents[]` | Each content has required `content_id`, `label`, `subject_mode`, `subject_roles`, and `when`. `content_id` uses the role-ID format and is unique across the entire model; `label` is 1–160 characters; `subject_mode` is `shared` or `per_person`; `subject_roles` is a non-empty list of unique declared role IDs. |
| `when` | `{"kind":"always"}` or `{"kind":"condition","condition_id":"<declared id>"}`. `condition_id` is forbidden for `always` and required for `condition`. A subject-scoped condition needs person context: a deliverable needs `output_mode: "per_person"`; a content needs `subject_mode: "per_person"` or a per-person parent deliverable. |
| `model_id` | UUID-format server-owned identity in a saved model. |
| `revision` | Positive integer server-owned revision in a saved model. |
| `model_hash` | SHA-256 over RFC 8785 JCS bytes of the complete saved model after removing only `model_hash`; no domain prefix. |
| `list_sha256` | `SHA-256("jz.anydoc.unsaved_private_list.v1\0" || JCS(authority.list))`, where the entire draft including `contract` is hashed. Do not substitute a plain JCS hash or a `models validate` draft hash. |

The compact serialized draft payload (the six draft fields, including
`contract`) is limited to 256 KiB; a saved model's `draft()` is checked against
the same limit. The private-list digest binds the exact typed draft and is not a
customer-content hash.

#### `authority.people[]`

Every binding is required to contain all three fields. IDs use the lower-case
schema identifier form `^[a-z][a-z0-9_]{0,63}$`; IDs are unique, and roles are
unique declared model role IDs. A `one`-cardinality role cannot be bound to more
than one person.

| Field | Shape and rules |
|---|---|
| `person_id` | Required unique local identifier. |
| `display_name` | Required, 1–160 characters, trimmed, no controls, slash, backslash, `.` or `..`; used only for rendering local output names. |
| `roles` | Required non-empty array of unique role IDs declared by `model.roles`. |

#### `authority.condition_results[]`

| Field | Presence | Shape and rules |
|---|---|---|
| `condition_id` | Required | Declared model condition. |
| `person_id` | Optional | Required for `subject`-scoped conditions and must identify a bound person; forbidden for `case`-scoped conditions. |
| `result` | Required | Boolean. |

Entries must be unique by `(condition_id, person_id)`. Missing answers do not
become `false`; they remain model-pending work.

#### `authority.scope`

The tagged object is exactly one of:

| `kind` | Fields | Rules |
|---|---|---|
| `all` | None | Select every expanded model output. |
| `selected` | Required `outputs` array | Each output reference has required `deliverable_id` and optional `person_id`. The array must be non-empty and unique; every reference must identify a known, applicable expanded output. |

#### `authority.action_mappings[]`

There must be exactly one ordered mapping per produced action output, with the
same output path and order as `actions` after output expansion. Every output
must be in the selected model scope, and its `deliverable_id` / optional
`person_id` must identify that model output. Source units in each output must
be assigned to claims exactly once.

| Mapping field | Presence | Shape and rules |
|---|---|---|
| `to` | Required | Must exactly equal one action's output path. Mapping paths are unique and follow action output order. |
| `deliverable_id` | Required | Declared model deliverable. |
| `person_id` | Optional | Bound person for a per-person output; absent for a single output. |
| `sources` | Required array | Each entry binds one source path/page set to one model content claim. |

Each `sources[]` entry has these fields:

| Source-claim field | Presence | Shape and rules |
|---|---|---|
| `from` | Required | Exact relative source path used by the corresponding action. |
| `pages` | Optional; defaults to `[]` | Empty means all action units for that `from`; otherwise positive, unique, ascending 1-based page numbers that exist in that action output. |
| `content_id` | Required | Declared content claim that belongs to the mapped output. |
| `subject_person_id` | Optional | Required to identify the expected person for a per-person claim; must not be set for a shared claim. |
| `observed_subject_person_id` | Optional | Person actually observed by the host; per-person evidence must match the expected person. |
| `subject_evidence` | Optional | `native_text`, `host_vision_ocr`, or `user_confirmed`. Per-person evidence without an observed subject is invalid; missing evidence/observation can remain pending. Shared claims must not carry person IDs or evidence. |

The compiler also refuses duplicate source-unit mappings, wrong-person reuse,
and outputs outside the chosen scope. `native_text` must correspond to usable
native text in the selected source units; it is not a claim available for every
file type.

### Public Guide context (`public_guide`)

`public_guide` is a separate top-level object, not authority detail. It is
required with `authority.kind = "public_guide"`; for comparison it must match
the selected private authority. The facts file stays local: the plan stores its
digest, the exact guide/revision pin, and the evaluation report.

| Context field | Presence | Shape and rules |
|---|---|---|
| `mode` | Required | `public_only_confirmed`, `private_comparison`, or `unsaved_private_list`. Public-only requires no comparison or `private_list_sha256`; the other modes require a comparison. |
| `guide_ref` | Required | Object below; provider must be `anycase`. |
| `pin` | Required | Exact revision object below. `payload_sha256` is a lowercase SHA-256 digest. |
| `status` | Required | `ready` or `pilot_partial`. |
| `verified_at` | Required | Non-empty verification timestamp string from the resolved revision. |
| `source_pins` | Required | Array of unique `vertical_id` / `evidence_revision_id` pairs. Each entry has both identifier fields. |
| `partial_scope_acknowledged` | Required | Boolean. Must be true for public-only use if status is `pilot_partial` or evaluation `scope_status` is not `complete`. |
| `as_of` | Required | Explicit ISO calendar date used by evaluation; no current-date default. |
| `jurisdiction` | Optional, nullable | String or `null`; absent or `null` means no jurisdiction was supplied and must match the evaluation context. |
| `locale` | Optional, nullable | String or `null`; absent or `null` means no locale was supplied and must match the evaluation context. |
| `facts_sha256` | Required | Lowercase SHA-256 of `"jz.anydoc.public_guide_facts.v1\0" || JCS(LocalFacts)`. Raw local facts are not part of the plan. |
| `private_list_sha256` | Optional | Present only for `unsaved_private_list`; must equal the selected `manual_private_list` `list_sha256`. |
| `evaluation` | Required | Complete vendor `EvaluationReport`; fields below. |
| `comparison` | Optional | Required for `private_comparison` and `unsaved_private_list`; forbidden for `public_only_confirmed`. Full shape below. |

`guide_ref` has exactly these required strings: `provider`, `pathway_id`,
`stage`, `channel`, and `document_list_id`. `provider` is exactly `anycase`; the
other four values are 1–256-byte ASCII identifiers using letters, digits,
`_`, `-`, `.`, or `:`.

`pin` has exactly these required fields: `program_release_id`,
`document_list_revision_id`, `payload_sha256`, `generation`, `schema_id`, and
`evaluator_abi`. Both IDs are valid identifiers; `payload_sha256` is lowercase
64-character hex; `generation` is an unsigned 64-bit integer; `schema_id` and
`evaluator_abi` are both `jz.document_requirements.v1`.

Each `source_pins[]` object has required `vertical_id` and
`evidence_revision_id`, both valid identifiers; duplicate pairs are rejected.

#### Nested `evaluation` (`EvaluationReport`)

The report schema is vendor type `jz.document_requirements.evaluation.v1`.
Every listed report field is required; optional nullable fields inside nested
objects are marked below. Report keys not in this schema are rejected.

| Report field | Shape and rules |
|---|---|
| `schema` | Exact `jz.document_requirements.evaluation.v1`. |
| `document_list_id` | Must equal `guide_ref.document_list_id`. |
| `document_list_sha256` | Must equal `pin.payload_sha256`, the digest of the validated pinned document-requirements payload. |
| `context` | Required evaluation-context object below. |
| `scope_status` | `complete`, `partial`, `unknown`, `context_incomplete`, or `out_of_scope`. |
| `facts_completeness` | `complete` or `incomplete`. |
| `requirements` | Array of requirement-evaluation objects below; may be empty when the validated public list has no requirements. |
| `result_sha256` | Lowercase 64-character SHA-256 result digest. It is calculated over JCS of the report fields except `result_sha256`, with prefix `jz.document_requirements.evaluation.v1\0`. |

The required `context` fields are `as_of` (ISO date), `pathway_id`, and `stage`;
`jurisdiction` and `locale` are optional nullable strings. Together these fields
must match the enclosing context's `as_of`, pathway, stage, jurisdiction, and
locale exactly.

Each `requirements[]` item has this typed shape. `instance_key` and
`local_subject_role` are optional nullable fields; all other listed fields are
required.

| Field | Shape and rules |
|---|---|
| `requirement_id` | String identifier from the validated payload: 1–128 ASCII bytes, alphanumeric or `_`, `-`, `.`, `:`. |
| `instance_key` | Optional; string or `null`; identifies an expanded repeat instance. A string is a stable local key of at most 256 bytes with no control characters. |
| `subject` | `{ "scope": "case"|"person"|"organization", "roles": [...] }`; case scope has no roles, person/organization scope has non-empty unique stable role identifiers (1–128 ASCII bytes, alphanumeric or `_`, `-`, `.`, `:`). |
| `subject_applicability` | String `"true"`, `"false"`, or `"unknown"` (not a JSON boolean). |
| `local_subject_role` | Optional; string or `null`. |
| `condition` | String `"true"`, `"false"`, or `"unknown"` (not a JSON boolean). |
| `exceptions` | Array of exception-evaluation objects. Each has `exception_id` (stable identifier), `condition` (string `"true"`/`"false"`/`"unknown"`), `effect` (`not_applicable`), and `unknown_facts` array. |
| `fulfillment` | Recursive evaluation object tagged by `op`; see below. |
| `unknown_facts` | Array of unresolved fact/path strings. |
| `status` | `required`, `not_applicable`, `conditional_unresolved`, `optional`, `on_request`, or `scope_incomplete`. |

`fulfillment` is tagged by `op`:

| `op` | Required fields |
|---|---|
| `document` or `translation` | `part_id` (stable identifier), `count` (integer 1–10,000), `shared` (boolean), `applicability` (string `"true"`/`"false"`/`"unknown"`), and `unknown_facts`. |
| `all_of` | `applicability` (string `"true"`/`"false"`/`"unknown"`), non-empty recursive `parts` array, and `unknown_facts`. |
| `one_of` | `applicability` (string `"true"`/`"false"`/`"unknown"`), recursive `parts` array with at least two alternatives, and `unknown_facts`. |

The pinned payload digest is
`SHA-256("jz.document_requirements.v1\0" || JCS(validated payload))`.
The `facts_sha256` hashes the complete typed local `LocalFacts` object
(`completeness`, `case_facts`, and `repeat_axes`), including empty maps; facts
itself is not copied into this context. These are provenance digests, not
placeholders to fill with arbitrary valid-looking hex.

#### Optional Public Guide comparison

For a new approval, the comparison must carry its basis. A basis-less historical
comparison may still be traceable with an old approval but is not sufficient
for new approval.

| Comparison field | Presence | Shape and rules |
|---|---|---|
| `official_pending` | Required | Array of official requirement keys still unresolved. |
| `private_pending` | Required | Array of local private-model/list work still unresolved. |
| `differences` | Required | Array of differences below. |
| `decisions` | Required | Array of decision records below; a fresh comparison has one decision per difference ID, without repeats. |
| `basis` | Optional historically; required for new approval | Comparison-basis object below. |
| `sha256` | Required | Lowercase digest of `JCS(comparison without sha256)` with prefix `jz.anydoc.public_guide_comparison.v1\0`. |

Each `differences[]` item requires `finding_id`, `kind`, and `requirement_id`;
`instance_key`, `content_id`, and `deliverable_id` are optional nullable strings.
`kind` is one of `official_requirement_unmapped`, `private_additional`,
`condition_difference`, `alternative_difference`, `format_or_grouping_difference`,
`unresolved_mapping`, or `case_official_difference`.
Each `decisions[]` entry is `{ "finding_id": "...", "decision": ... }`, where
`decision` is `include`, `exclude`, `defer`, or `use_private_model`.

`basis` requires `schema` (`jz.anydoc.guide_comparison_basis.v1`), `guide_ref`,
`pin`, `facts_sha256`, `evaluation_sha256`, `private_source`, and
`bindings_sha256`. Its guide ref/pin/facts digest must match the enclosing
context; `evaluation_sha256` must equal `evaluation.result_sha256`. The tagged
`private_source` is either:

- `{"kind":"saved_model","case_type":"...","model_id":"...","revision":1,"model_hash":"...","draft_sha256":"..."}` for `private_comparison`; or
- `{"kind":"unsaved_private_list","list_sha256":"..."}` for `unsaved_private_list`.

All basis digests are lowercase SHA-256. `draft_sha256` is the domain-separated
private-list digest of the saved model's `draft()` (excluding server-owned
identity fields). `bindings_sha256` is
`SHA-256("jz.anydoc.public_guide_bindings.v1\0" || JCS(bindings sorted by
requirement_id, instance_key, deliverable_id, content_id, person_id, and
private_condition))`. A binding has required `requirement_id`, `deliverable_id`,
and `content_id`; `instance_key`, `person_id`, and `private_condition` are
optional. Saved-model identity/hash must match the selected `authority.model`;
the unsaved list hash must match both the selected `authority.list` digest and
`private_list_sha256`. Comparison context is not public-only authority.

### Source accounting and exclusions

Actions are the included set. A source path cannot be both included and
excluded. Every exclusion must reference a current, canonical relative source
path; it may appear only once. `related_source`, when required, must directly
name the appropriate included source. Any other file in the input inventory is
source-pending until accounted for; pending sources block approval. Deterministic
OS metadata (`.DS_Store`, `Thumbs.db`, and `._*`) is ignored unless explicitly
referenced. Symlinks in the scanned source tree are refused.

| Exclusion field | Presence | Shape and rules |
|---|---|---|
| `from` | Required | Canonical relative source path in the current input inventory. |
| `reason` | Required | `information_collection`, `superseded_editable_source`, `duplicate`, or `user_excluded`. |
| `related_source` | Optional in schema | Required only for the two reasons below that name a canonical source; otherwise forbidden. |
| `evidence` | Optional in schema | `native_text`, `host_vision_ocr`, or `user_confirmed`; reason-specific requirements below apply. |

| `reason` | Required condition |
|---|---|
| `information_collection` | Requires `evidence`; forbids `related_source`. Native-text evidence must actually exist. Intake questionnaires and collection material stay out of every output, regardless of format. |
| `superseded_editable_source` | Requires evidence and `related_source`. `from` must be an Office document; `related_source` must directly identify an included final PDF. |
| `duplicate` | Requires `related_source`; forbids `evidence`. The target must be a different, directly included source with identical SHA-256. |
| `user_excluded` | Requires `evidence: "user_confirmed"`; forbids `related_source`. |

Exclusion records do not delete, move, or edit originals. Native-text evidence
is refused for an image or a source without usable native text; unreadable
sources remain pending instead of becoming eligible for exclusion.

### Action shapes

All action objects require their exact `op` and required fields below; unknown
keys are rejected. All `from` values are existing relative paths under
`input_root`, not absolute paths, traversal, `.anydoc-work`, or `anydoc-output`.
All `to` values are relative portable output paths without traversal or
surrounding whitespace; collisions across supported macOS/Windows filename
rules are refused. Copy/rename actions preserve bytes. Encrypted, XFA, AcroForm,
or digitally signed PDFs are copy/rename-only; PDF page operations and
compression refuse them.

| `op` | Required fields | Optional fields / rules |
|---|---|---|
| `copy`, `rename`, `copy_and_rename` | `op`, `from`, `to` | `preserve_bytes` defaults to `true` and must be `true`; setting false is refused. |
| `select_pdf_pages` | `op`, `from`, non-empty `pages`, `to` | `pages` are 1-based page numbers within the PDF, in the order selected. |
| `split_pdf` | `op`, `from`, non-empty `ranges`, matching-length `to` | Each range is `[start,end]` with 1-based inclusive bounds (`start >= 1`, `end >= start`) and must be within the PDF. |
| `merge_pdf` | `op`, non-empty `from` array, `to` | Input order determines merge order. |
| `reorder_pages` | `op`, `from`, non-empty `pages`, `to` | `pages` are 1-based page numbers within the PDF, in the requested output order. |
| `rotate_pages` | `op`, `from`, non-empty `pages`, `degrees`, `to` | `pages` are 1-based page numbers within the PDF. `degrees` is exactly `90`, `180`, or `270`; listed pages rotate, other pages stay in the output. |
| `image_to_pdf` | `op`, `from`, `to` | `from` is one path or a non-empty array of paths; array order is preserved. JPEG, PNG, WebP, and TIFF are supported; HEIC is refused. JPEG is not recompressed. |
| `office_to_pdf` | `op`, `from`, `to` | DOC, DOCX, RTF, XLSX, PPTX, and ODT only, with the Office converter installed. Macros and remote links are refused; encrypted Office is copy-only. |
| `compress_to_explicit_budget` | `op`, `from`, `to`, `max_bytes` | `max_bytes` must be greater than zero. Optional `clarity_tier`: `clarity_priority` (default), `balanced`, or `max_shrink`. Never bypasses the chosen clarity floor. |
| `normalize_page_size` | `op`, `from`, `to`, and either `paper` or both `width_pt` and `height_pt` | `paper` is `letter` or `a4`; if dimensions are also supplied, they must match the paper within 0.05 pt. Dimensions are finite points in `(0,100000]`. Optional `orientation_policy`: `preserve_displayed` (default), `force_portrait`, or `force_landscape`. |
| `render_photo_document` | `op`, `to`, `layout`, non-empty `items` | `layout` is 1, 2, or 4. `fit` defaults to and must be `fit`. Page size uses `paper` (`letter`/`a4`), both `page_width_pt` and `page_height_pt`, or neither (default letter); explicit dimensions with paper must match. Each item requires `from`; `caption` is optional and defaults to empty string. JPEG/PNG/WebP/TIFF only; PDFs and HEIC are refused. |

For `render_photo_document`, `items` are `{ "from": "...", "caption": "..." }`
objects in approved order. Photos fit their cells without crop; captions do not
change the approved layout. `normalize_page_size` uses the corresponding
`width_pt` / `height_pt` field names, not the photo action's `page_width_pt` /
`page_height_pt` names.

## Examples

Each complete plan below can be validated offline with a temporary input
containing synthetic `source.txt` bytes. The examples use
`constraints.max_file_bytes: null` to declare that no external byte cap was
provided; substitute a cited cap only when the real portal/guide states one.

### Example: manual_plan

```json
{
  "version": 4,
  "authority": { "kind": "manual_plan" },
  "input_root": ".",
  "output_dir": "anydoc-output/v001",
  "actions": [
    {
      "op": "copy",
      "from": "source.txt",
      "to": "Source.txt",
      "preserve_bytes": true
    }
  ],
  "excluded_sources": [],
  "constraints": { "max_file_bytes": null }
}
```

### Example: manual_private_list

`list_sha256` below is `SHA-256("jz.anydoc.unsaved_private_list.v1\0" ||
JCS(list))` over the entire embedded draft, including `contract`.

```json
{
  "version": 4,
  "authority": {
    "kind": "manual_private_list",
    "list": {
      "contract": "anydoc-assembly-model-v2",
      "case_type": "example.synthetic",
      "title": "Synthetic example list",
      "roles": [
        { "role_id": "applicant", "label": "Applicant", "cardinality": "many" }
      ],
      "conditions": [],
      "deliverables": [
        {
          "deliverable_id": "bundle",
          "sequence": "01",
          "filename_template": "Source.txt",
          "output_mode": "single",
          "serves_roles": ["applicant"],
          "when": { "kind": "always" },
          "contents": [
            {
              "content_id": "sample_content",
              "label": "Synthetic source content",
              "subject_mode": "shared",
              "subject_roles": ["applicant"],
              "when": { "kind": "always" }
            }
          ]
        }
      ]
    },
    "list_sha256": "c18ee80f7f0d7a1e24ccb1a3cec9169a487a8649ff5305c75d01efdf78ed9d06"
  },
  "input_root": ".",
  "output_dir": "anydoc-output/v001",
  "actions": [
    {
      "op": "copy",
      "from": "source.txt",
      "to": "Source.txt",
      "preserve_bytes": true
    }
  ],
  "excluded_sources": [],
  "constraints": { "max_file_bytes": null }
}
```

### Example: public_guide

This offline synthetic context uses a valid, pinned one-requirement public-list
evaluation, an empty complete local-facts object, and their computed
integrity digests. The payload and facts are not plan fields and are not sent to
a service by `plan validate`; the plan carries the revision pin, facts digest,
and full evaluation report.

```json
{
  "version": 4,
  "authority": { "kind": "public_guide" },
  "input_root": ".",
  "output_dir": "anydoc-output/v001",
  "actions": [
    {
      "op": "copy",
      "from": "source.txt",
      "to": "Source.txt",
      "preserve_bytes": true
    }
  ],
  "excluded_sources": [],
  "constraints": { "max_file_bytes": null },
  "public_guide": {
    "mode": "public_only_confirmed",
    "guide_ref": {
      "provider": "anycase",
      "pathway_id": "path.synthetic",
      "stage": "stage.synthetic",
      "channel": "default",
      "document_list_id": "list.synthetic"
    },
    "pin": {
      "program_release_id": "release.synthetic",
      "document_list_revision_id": "revision.synthetic",
      "payload_sha256": "7148c9d2fef8a26c6195fee487f6b1c687830bb4c1d9139bf4f23d35a470ddc5",
      "generation": 1,
      "schema_id": "jz.document_requirements.v1",
      "evaluator_abi": "jz.document_requirements.v1"
    },
    "status": "ready",
    "verified_at": "2026-09-28T12:00:00Z",
    "source_pins": [],
    "partial_scope_acknowledged": false,
    "as_of": "2026-09-28",
    "jurisdiction": "CA",
    "locale": "en-CA",
    "facts_sha256": "9d7142b934ccda9fea03d9704927ee0c2358da2d16b9c440b141fad152323f13",
    "evaluation": {
      "schema": "jz.document_requirements.evaluation.v1",
      "document_list_id": "list.synthetic",
      "document_list_sha256": "7148c9d2fef8a26c6195fee487f6b1c687830bb4c1d9139bf4f23d35a470ddc5",
      "context": {
        "as_of": "2026-09-28",
        "pathway_id": "path.synthetic",
        "stage": "stage.synthetic",
        "jurisdiction": "CA",
        "locale": "en-CA"
      },
      "scope_status": "complete",
      "facts_completeness": "complete",
      "requirements": [
        {
          "requirement_id": "passport_bio_page",
          "instance_key": null,
          "subject": { "scope": "case", "roles": [] },
          "subject_applicability": "true",
          "local_subject_role": null,
          "condition": "true",
          "exceptions": [],
          "fulfillment": {
            "op": "document",
            "part_id": "part.original",
            "count": 1,
            "shared": false,
            "applicability": "true",
            "unknown_facts": []
          },
          "unknown_facts": [],
          "status": "required"
        }
      ],
      "result_sha256": "768d873b2d9cc32f82815816f858606bcdcd62372d0c0b4b7b50b128eec72666"
    }
  }
}
```

### Example: private_model

The saved model hash below is SHA-256 of JCS for the model object with only
`model_hash` removed. Its one content claim is shared; the plan binds one
synthetic person to the declared role and maps the action source to that claim.

```json
{
  "version": 4,
  "authority": {
    "kind": "private_model",
    "model": {
      "model_id": "123e4567-e89b-12d3-a456-426614174000",
      "contract": "anydoc-assembly-model-v2",
      "case_type": "example.synthetic",
      "title": "Synthetic example model",
      "revision": 1,
      "roles": [
        { "role_id": "applicant", "label": "Applicant", "cardinality": "many" }
      ],
      "conditions": [],
      "deliverables": [
        {
          "deliverable_id": "bundle",
          "sequence": "01",
          "filename_template": "Source.txt",
          "output_mode": "single",
          "serves_roles": ["applicant"],
          "when": { "kind": "always" },
          "contents": [
            {
              "content_id": "sample_content",
              "label": "Synthetic source content",
              "subject_mode": "shared",
              "subject_roles": ["applicant"],
              "when": { "kind": "always" }
            }
          ]
        }
      ],
      "model_hash": "8e4b990e619ce005ce0ecf621d5fae8ae062695e64684fa676431f3deccc8f62"
    },
    "people": [
      { "person_id": "alex", "display_name": "Alex Example", "roles": ["applicant"] }
    ],
    "condition_results": [],
    "scope": { "kind": "all" },
    "action_mappings": [
      {
        "to": "Source.txt",
        "deliverable_id": "bundle",
        "sources": [
          { "from": "source.txt", "content_id": "sample_content" }
        ]
      }
    ]
  },
  "input_root": ".",
  "output_dir": "anydoc-output/v001",
  "actions": [
    {
      "op": "copy",
      "from": "source.txt",
      "to": "Source.txt",
      "preserve_bytes": true
    }
  ],
  "excluded_sources": [],
  "constraints": { "max_file_bytes": null }
}
```

## Hash and size rules at a glance

| Item | Digest / limit |
|---|---|
| Plan v4 approval identity | `SHA-256("jz.anydoc.plan.v4\0" || JCS(original plan JSON))`; object-key order and whitespace do not change it. Any key/value change, including adding a previously omitted local `contract`, changes the hash. |
| Saved `authority.model.model_hash` | SHA-256 of JCS of the complete saved model with only `model_hash` removed; no domain prefix. |
| `authority.list_sha256` | `SHA-256("jz.anydoc.unsaved_private_list.v1\0" || JCS(complete draft))`. |
| Public payload pin | `SHA-256("jz.document_requirements.v1\0" || JCS(validated payload))`. |
| Public facts digest | `SHA-256("jz.anydoc.public_guide_facts.v1\0" || JCS(typed LocalFacts))`. |
| Public evaluation result | `SHA-256("jz.document_requirements.evaluation.v1\0" || JCS(report fields except result_sha256))`. |
| Public comparison digest | `SHA-256("jz.anydoc.public_guide_comparison.v1\0" || JCS(comparison except sha256))`. |
| Model/list draft fields | At most 256 KiB. |
| Public payload / local facts | Each at most 1 MiB. |

`\0` in the formulas above denotes one NUL byte after the domain text, not the
two visible characters backslash and zero. JCS means RFC 8785 JSON Canonicalization
Scheme. The plan digest uses the original plan value; locally decoded contract
defaults never rewrite provenance or approval identity.
