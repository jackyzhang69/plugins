---
name: anydoc-assemble
description: >-
  Pack a messy local document folder using only the AnyDoc CLI: inspect facts,
  write a fully expanded plan, get the human to approve, assemble, verify.
  Manual plans are offline-capable. Saved assembly models use connect-anydoc.
  The host agent and the user own all semantic judgment.
when_to_use: |-
  "pack this folder", "assemble the upload package", "inspect this client
  directory", "rename and merge these PDFs", "build the IRCC document folder".
---

# AnyDoc assemble — host agent contract

AnyDoc organizes the final forms and supporting documents that the user intends
to submit to IRCC or another receiving institution. Its native reader inspects
every supported source first, extracts available page text, and records previews
and protection facts before the host makes semantic decisions. Consume that
batch evidence; do not reopen every normally readable document. Use the active
host's already-authorized vision/OCR only for scans or images with no usable
text, encrypted/unsupported content, XFA placeholder-only pages, or remaining
identity/meaning ambiguity. Never send customer material to a second model,
subagent, reviewer, feedback, telemetry, or another product. Never say a package
is ready to submit.

The binary does not classify documents or run OCR. The host records the semantic
decision and its evidence basis; AnyDoc then enforces exact source accounting,
person/claim consistency, and approval freshness.

Resolve the binary once per session, then export `ANYDOC_BIN`:

1. `$ANYDOC_BIN` if already set.
2. Canonical install: `$JACKYZHANG_APP_HOME/plugins/anydoc/current/bin/darwin-arm64/anydoc` on macOS Apple Silicon, or `%USERPROFILE%\.jackyzhang.app\plugins\anydoc\current\bin\win32-x64\anydoc.exe` on Windows x64. Never use a binary for a different platform.
3. If that tree is missing, run `"$PACKAGE_BIN" doctor --repair-install --json` from this marketplace package, then use the canonical path.
4. Do not prefer a random `anydoc` on PATH over the canonical tree.

Runtime data is `~/.jackyzhang.app/anydoc/`. Credentials are `~/.jackyzhang.app/token/user.json`. Never a second product token file.

## Talk to the human (mandatory)

When speaking to the person in the chat (not when writing tool args):

1. **Plain language.** Everyday words. Do not lead with flags, JSON, or file hashes.
2. **What / next, not how.** Say what you found and what they must confirm. Do not narrate every CLI flag.
3. **Major stages only.** inspect done → proposed packing list → waiting for their yes → files written → mechanical check done.
4. **They decide.** Names, page order, what to keep or drop, and whether the list is right. You never silently drop a file.
5. **JSON is for tools.** Translate results into one or two sentences. Never paste credentials.
6. **Forbidden claims.** Do not tell the user a package can be filed, that an application is complete, or that a required document is missing. If the Office converter is missing, say: export Word/Excel/PowerPoint to PDF first. If a file has macros or remote links, say: this version will not convert it. If an iPhone photo is HEIC, say: export JPEG or PNG first.

## Non-negotiable rules

1. **All packing goes through the AnyDoc CLI.** Do not copy files yourself to “help.”
2. **Workflow commands are only AnyDoc.** Do not resolve or execute another product's binary. Use only the active host's already-authorized reading/vision/OCR capability to understand files; do not delegate customer content to another model or agent.
3. **Login follows the chosen authority.** Inspect and an explicitly user-provided `manual_plan` stay offline. Resolving or changing a saved private model requires **connect-anydoc**. If `token/user.json` already exists from any official plugin, do not ask them to log in again.
4. **Approve before assemble.** Unapproved plans are refused. A file added,
   removed, renamed, or replaced after approval invalidates the old approval.
5. **Plan must be fully expanded.** Concrete `from` / `to` / page lists. No “merge the important ones.”
6. **Encrypted, form, or signed PDFs:** copy or rename only. Do not split, merge, rotate, compress, normalize, or put them on a photo sheet.
7. **Do not ask the AnyDoc binary to OCR.** Use its native text first; read only
   unresolved scans visually in this primary session. Do not invent a subject,
   caption, category, exclusion reason, or condition result.

## Choose the assembly authority first

Before inspecting or organizing a model-bound case, identify one exact `case_type` and check the user's accountd-hosted private model:

```bash
"$ANYDOC_BIN" models resolve --case-type <exact-case-type> --json
```

Treat the returned state literally:

| State | Required host action |
|---|---|
| `model_found` | Show its name and revision. Use that exact model, and bind its `model_id`, `revision`, and `model_hash` into the plan. Only here may you say the work follows the user's saved habit. |
| `model_absent` | Start **Teach Me** below. This is the only state that permits Teach Me. |
| `model_unavailable` | Stop the model flow. Say the saved model cannot be checked now. Do not call it absent, do not Teach Me, and do not silently switch to a manual plan. |
| `model_invalid` | Stop. Say the saved model response failed validation. Do not use it or Teach Me. |
| exact case type is ambiguous | This is the host state `model_ambiguous`: show the concrete choices and ask the human. Do not guess and do not Teach Me. |

A Public Guide is visible reference material only. Show any conflict between it and the private model and pause for the human's decision. A Guide never replaces a document list and never becomes hidden authority.

The only offline alternative is a complete `manual_plan` explicitly supplied or approved by the user as the authority. Backend failure never authorizes this fallback, and the host must label it `manual_plan`, not “saved habit.”

## Teach Me (only after `model_absent`)

1. Ask for one representative **document list** from the same case type. Do not infer a reusable model from the current messy folder, a finished package, or a Public Guide alone.
2. Locally extract only the reusable structure: ordered final deliverables, what content each deliverable contains, which role needs it, and the named condition for when it applies.
3. Remove RCIC receipt/check marks, provided/missing status, customer names, file paths, current-case facts, role bindings, condition results, and execution actions. These never go to accountd.
4. Build a strict `anydoc-assembly-model-v2` draft and run `models validate --model <draft.json> --json` offline. Explicitly choose `single|per_person`, the exact filename template, `shared|per_person` content ownership, and `case|subject` condition scope.
5. Show the **entire** model to the human in plain language: every ordered output filename pattern, included content, role, and named condition. Ask for explicit confirmation; partial summaries are not confirmation.
6. Only after that yes, run `models save --model <draft.json> --user-confirmed --json`. Report the returned `model_id`, `revision`, and `model_hash`. Use `models replace` with `--expected-revision` for later full replacements; use `models forget` only after a separate explicit confirmation.

The saved asset is an abstract model, never a case record. Do not upload the document list itself or any client document.

## End-to-end

Work inside the user’s folder. Keep local semantic artifacts under
`<input>/.anydoc-work/`: `inspection.json`, `document-map.json`,
`assembly-plan.json`, and `approval-receipt.json`. Final snapshots go only to
`<input>/anydoc-output/v001`, then `v002`, `v003`, and so on. Never overwrite a
snapshot or alter the user's originals.

```bash
"$ANYDOC_BIN" doctor --json
"$ANYDOC_BIN" inspect --input /absolute/folder --json
```

Read the inspection JSON first. For a page with usable native text, use that
evidence directly. Open/render only unresolved pages. For every per-person
source, record the stable local `person_id` actually supported by the content
and `subject_evidence=native_text|host_vision_ocr|user_confirmed`. A filename is
not proof of ownership. Office text produced through the sandboxed converter is
reported as converted native evidence with the converter identity. Conversion
failure stays unreadable/pending; it never becomes permission to exclude.

Write `document-map.json` (`generated_by=host_agent`) with summaries, suggested
splits, and observed subjects. Then write a fully expanded Plan v3.

Its `authority.kind` is exactly one of:

- `manual_plan`, for a complete plan explicitly supplied or approved as the basis by the user.
- `private_model`, embedding the complete validated v2 model plus local
  `people`, scoped `condition_results`, `scope`, and `action_mappings`. Each
  source mapping binds pages to one model claim and, for per-person claims,
  records both `subject_person_id` and the host's
  `observed_subject_person_id`.

Plan v3 also has required `excluded_sources[]`. Actions are the included set;
anything else currently in the folder is pending unless it appears in this
explicit exclusion list or is reported as deterministic OS metadata. Reasons
are only `information_collection`, `superseded_editable_source`, `duplicate`,
or `user_excluded`:

- Information-collection worksheets, questionnaires, checklists, internal
  notes, and working drafts are excluded from the submission assembly by
  default when the content evidence supports that judgment.
- An editable Office source for an already-final PDF is excluded by default
  only when it points to that included final PDF and the evidence basis is
  recorded. A flattened or wet signature is valid evidence; a digital-signature
  field is not required.
- Exact duplicates must have identical SHA-256 and point directly to the
  included canonical source.
- `user_excluded` requires the user's explicit confirmation.

If the user specifically asks to include one of these files, put it in an
action instead; included and excluded can never overlap. Exclusion never
deletes, moves, or edits the original and never claims legal irrelevance.

For `private_model`, every produced action unit is mapped exactly once. The
compiler checks exact output count/name, per-person conditions, role
cardinality, subject identity, claim ownership, and cross-person reuse.
Uncollected model material is `model pending`, not an invalid case: validate/show
may succeed while it is pending; `scope=selected` can finish one known person's
output without pretending absent materials were collected. A file that is
actually present but neither included nor excluded is separately `source
pending` and always blocks approval. Plan v1/v2 is rejected and regenerated,
never silently upgraded.

```bash
"$ANYDOC_BIN" plan validate --input /absolute/folder --plan /absolute/assembly-plan.json --json
"$ANYDOC_BIN" plan show --input /absolute/folder --plan /absolute/assembly-plan.json --json
```

Show the human the packing list in plain language, including estimated pages/size when the tool gave numbers, and `unknown` when it did not. Wait for an explicit yes.

```bash
"$ANYDOC_BIN" plan approve --input /absolute/folder --plan /absolute/assembly-plan.json --receipt /absolute/approval-receipt.json --json
"$ANYDOC_BIN" assemble --input /absolute/folder --plan /absolute/assembly-plan.json --receipt /absolute/approval-receipt.json --json
"$ANYDOC_BIN" verify --input /absolute/folder --plan /absolute/assembly-plan.json --receipt /absolute/approval-receipt.json --json
```

If assemble was interrupted: `resume --input …`. To drop scratch files only:
`clean --input …`. It preserves the inspection, document map, plans, receipts,
output history, sources, and all `anydoc-output/vNNN` snapshots. Re-inspection
skips both AnyDoc roots and flags copied-back prior outputs.

## Plan actions this version can execute

| op | Meaning |
|---|---|
| `copy` / `rename` / `copy_and_rename` | Preserve bytes into a new immutable snapshot such as `anydoc-output/v001` |
| `select_pdf_pages` / `split_pdf` / `merge_pdf` / `reorder_pages` / `rotate_pages` | Unencrypted, unsigned, non-form PDFs only |
| `image_to_pdf` | JPEG, PNG, WebP, TIFF. JPEG is not recompressed. HEIC is refused |
| `office_to_pdf` | DOC, DOCX, RTF, XLSX, PPTX, ODT when the Office converter is present. Macros and remote links are refused. Encrypted Office is copy-only. No text-reflow fallback. |
| `compress_to_explicit_budget` | Unencrypted, unsigned, non-form PDFs only. Requires `max_bytes`. Rewrites embedded rasters (downsample + JPEG). Already under the budget is copied as-is. If the budget cannot be reached, the tool fails and does not shrink silently or split the file. |
| `normalize_page_size` | Unencrypted, unsigned, non-form PDFs only. Requires `width_pt` and `height_pt`, or `paper` (`letter` = 612×792, `a4` = 595.28×841.89). Fits each page into that box, keeps aspect ratio, letterbox, no crop, no auto-rotate. |
| `render_photo_document` | Photo sheet. `layout` is 1, 2, or 4 cells; extra photos wrap to the next page with the same grid. `fit` is `fit` only (keep aspect ratio, letterbox, no crop). Order and captions as written. Caption overflow is a preview warning; it does not change the approved cell count. JPEG/PNG/WebP/TIFF. HEIC is refused. PDFs are not photo items. Same approve hash as any other action. A standalone `photo-plan.json` with this op at the top level is also accepted. |

`constraints.max_file_bytes`: if a result would be over the cap, the tool fails. This version can compress only when the plan includes `compress_to_explicit_budget` with an explicit byte budget; if it cannot reach that budget, it fails and does not shrink silently.

## Honesty table (say this to the human)

| Situation | What to say |
|---|---|
| Word / Excel / PowerPoint | This version can convert Office to PDF when the converter is installed. Files with macros or remote links are refused. Encrypted Office is copied as-is. If the converter is missing, export PDF yourself. |
| HEIC photo | This version cannot convert HEIC. Export JPEG or PNG first. |
| Encrypted official PDF | It will be copied as-is. It will not be split. |
| File over a portal size | This version compresses only when the packing list names an explicit byte budget. If it still cannot reach that size, it fails. It will not split the file for you. |
| Scan / photo, no text | You must inspect it with the active host's authorized vision/OCR in this primary session. The AnyDoc binary does not read the picture, and customer content cannot go to another agent/model. |
| Relationship photos | The packing list names the order, captions, and 1/2/4 grid. The tool fits each photo in its cell and does not crop or invent captions. If a caption is too long, that is a warning — the grid stays as approved. |
| Verify ok | The pack matches the approved list. That is not permission to file anything. |

## Self-intro

- Inspect a messy folder of PDFs, photos, and Office files.
- You and the user choose names, order, and what to keep.
- After you approve the list, AnyDoc builds a new folder of copies and assembled PDFs.
- Explicit manual plans are offline-capable. Saved private models are accountd-hosted and require `connect-anydoc` to resolve. Not a lawyer. Not a form filler.
