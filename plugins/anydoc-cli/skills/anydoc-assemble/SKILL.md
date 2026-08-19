---
name: anydoc-assemble
description: >-
  Pack a messy local document folder using only the AnyDoc CLI: inspect facts,
  write a fully expanded plan, get the human to approve, assemble, verify.
  Offline-capable. Login is optional via connect-anydoc (Tell-Jacky / guides /
  Remember Me). The host agent and the user own all semantic judgment.
when_to_use: |-
  "pack this folder", "assemble the upload package", "inspect this client
  directory", "rename and merge these PDFs", "build the IRCC document folder".
---

# AnyDoc assemble — host agent contract

AnyDoc inspects mechanical facts and executes an **approved** packing list.
It does not classify documents, does not OCR, does not fill official forms,
and never says a package is ready to submit.

Resolve the binary: `$ANYDOC_BIN` if set, otherwise `anydoc` on PATH.
On Windows use `anydoc.exe`. Export `ANYDOC_BIN` once per session.

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
2. **Workflow commands are only AnyDoc.** Do not instruct the host to resolve or execute another product’s binary. If this machine happens to have some other local text-extraction capability, you may use it as an optional aid to *understand* a file. It is never a required step, never named as a product, and never written into the packing workflow.
3. **Login is optional.** Inspect / plan / assemble / verify stay offline and do not require an account. Use **connect-anydoc** only when the human wants Tell-Jacky, `whoami`, Guides, or Remember Me. If `token/user.json` already exists from any official plugin, do not ask them to log in again.
4. **Approve before assemble.** Unapproved plans are refused.
5. **Plan must be fully expanded.** Concrete `from` / `to` / page lists. No “merge the important ones.”
6. **Encrypted, form, or signed PDFs:** copy or rename only. Do not split, merge, rotate, compress, normalize, or put them on a photo sheet.
7. **Do not OCR. Do not call a model to invent captions or categories.**

## End-to-end (this version, offline-capable)

Work inside the user’s folder. Prefer writing `inspection.json`, `document-map.json`, `assembly-plan.json`, and `approval-receipt.json` next to the materials (not inside the delivered subdirectory).

```bash
"$ANYDOC_BIN" doctor --json
"$ANYDOC_BIN" inspect --input /absolute/folder --json
```

Read the JSON. For every file, either you can describe it to the human or the tool already marked the unit unreadable. When the Office converter is installed, Office pages are real pages; when it is missing, Office units stay `office_pages_require_renderer` (that is not silent).

You write `document-map.json` (`generated_by=host_agent`) — summaries and suggested splits. You write a fully expanded `assembly-plan.json`.

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

If assemble was interrupted: `resume --input …`. To drop scratch files only: `clean --input …` (does not delete sources or the delivered folder).

## Plan actions this version can execute

| op | Meaning |
|---|---|
| `copy` / `rename` / `copy_and_rename` | Preserve bytes into a **new** subdirectory (default `anydoc-assembled`) |
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
| Scan / photo, no text | You look at the file (or a preview if present). The tool does not read the picture. |
| Relationship photos | The packing list names the order, captions, and 1/2/4 grid. The tool fits each photo in its cell and does not crop or invent captions. If a caption is too long, that is a warning — the grid stays as approved. |
| Verify ok | The pack matches the approved list. That is not permission to file anything. |

## Self-intro

- Inspect a messy folder of PDFs, photos, and Office files.
- You and the user choose names, order, and what to keep.
- After you approve the list, AnyDoc builds a new folder of copies and assembled PDFs.
- Offline-capable. Login is optional (`connect-anydoc`) and is not part of packing. Not a lawyer. Not a form filler.
