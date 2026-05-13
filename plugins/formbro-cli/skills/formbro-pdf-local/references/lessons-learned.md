# Lessons Learned — Plugin PDF Development

Compiled from the 2026-05-12 extraction session covering 15/15 IRCC forms with pypdf parity 0/0.
Real traps, real fixes — every item below was hit in production development.

---

## The 4 Fill Pathways (full details)

### Pathway 1 — Template-mutation (bespoke IMM0008)

Load pristine datasets XML from source PDF, parse to DOM, mutate at known XFA paths (`MAIN_FIELD_PATHS` — approximately 400 deep tuples), re-serialize, incremental inject.

**When to use**: forms with deep XFA template binding (`<form1><Page1><Section><qN><Field>`). IMM0008 is the canonical example. The key diagnostic: the datasets packet exists but the XFA binding paths are highly form-specific and don't map cleanly to flat field names.

**Files involved**:
- `pdf/pdf-fill-xfa/src/bespoke/imm0008/template_mutation.rs` — DOM engine: walks the datasets XML tree, matches path tuples, writes leaf values
- `pdf/pdf-fill-xfa/src/bespoke/imm0008/apply.rs` — `MAIN_FIELD_PATHS` constant + `apply_main_payload` function that drives mutation
- `pdf/pdf-fill-xfa/src/bespoke/imm0008/embedded_template.rs` — pristine datasets XML held as a Rust constant (avoids reading from disk at fill time)
- `pdf/pdf-fill-xfa/src/bespoke/imm0008/lov_lookup.rs` — LOV table parser mapping human labels to IRCC numeric codes

**End step**: after template mutation, write output via `fill_pdf_incremental_with_datasets` (incremental.rs), then pass through `fill_with_bun_normalize` to materialize AcroForm /V widgets.

**Pitfalls**:
- Forgetting to call `normalize_imm0008_payload` before mutation — results in raw human labels landing in XFA instead of codes
- Payload key drift: sections expect flat dot-notation but callers pass nested objects — see Trap 1

---

### Pathway 2 — XFA-packet-inject (bespoke IMM5406)

Source PDF lacks a datasets packet entirely. Build a datasets XML document from scratch, insert into the PDF's `/XFA` array as a new stream object via lopdf.

**When to use**: form's XFA array has only `preamble`, `config`, `template`, `localeSet` — no `datasets` stream (IMM5406, some older IRCC forms). Confirmed by the Step 1 diagnostic in `add-new-form.md` showing no `datasets` entry.

**Files involved**:
- `pdf/pdf-fill-xfa/src/incremental.rs::inject_xfa_packet` — inserts a new `(name, stream)` pair into the `/XFA` array; handles both encrypted and unencrypted PDFs
- `pdf/pdf-fill-xfa/src/bespoke/imm5406.rs` — form-specific field path definitions and the synthesized datasets XML builder

**End step**: always `fill_with_bun_normalize` after injection.

**Pitfalls**:
- V4/R4 AES encryption — lopdf cannot mutate encrypted streams in place; must use a qpdf-flattened copy as the injection target (Trap 11)
- XFA packet name must be `Object::String(b"datasets", StringFormat::Literal)`, NOT `Object::Name(b"datasets")` — silent failure if wrong
- LOV codes for IMM5406 are NOT the same as IMM0008 (e.g., `5` = Married in 5406 vs `02` in 0008) — see Trap 2

---

### Pathway 3 — Datasets-rewrite (12 generic forms via filler.rs)

Source PDF already has a datasets stream. Walk payload keys × `form_<id>.json` field specs × `dataset_paths_<id>.json` XPath map. For each key, locate the corresponding XML leaf node in the datasets stream and overwrite its text content. Re-serialize and write back to the PDF via incremental update.

**When to use**: standard XFA forms that already ship with a datasets stream (12 of the 15 IRCC forms fall here). Confirmed when Step 1 diagnostic shows `datasets` in the XFA packet list.

**Files involved**:
- `pdf/pdf-fill-xfa/src/filler.rs::fill_pdf` — 788 LOC core engine; handles all type coercions, LOV lookup, date splitting, city/province combos, radio_check complement paths
- `pdf/vendor/forms/form_<id>.json` — field spec: `{key: {type, values?, ...}}`
- `pdf/vendor/forms/dataset_paths_<id>.json` — XPath mapping: `{flat_key: "xfa[0].datasets[0].data[0].form1[0]...leaf[0]"}`

**End step**: after `fill_pdf`, pass output through `fill_with_bun_normalize`. This is non-optional — without it, macOS Preview renders blank fields (Trap 4).

**Pitfalls**:
- `select_city` returns a JSON object `{"province": "BC", "city": "Vancouver"}` — use `as_str()` for the province sub-key, not `as_u64()` (silent None)
- `radio_check` fields need a complement path `<key>:no` in dataset_paths so the "unselected" sibling gets cleared — otherwise stale values persist from the pristine datasets
- AcroForm /V materialization: Pathway 3 alone is not sufficient for Preview rendering (Trap 4)

---

### Pathway 4 — Setvalue+Bun (IMM5669)

Source PDF has no datasets stream AND synthesizing one is not viable. Instead, emit a list of `(dataId, value)` operations. Pass these to pdfjs via `annotationStorage.setValue` calls, then `saveDocument()` writes both the XFA datasets layer and the AcroForm /V widgets fresh.

**When to use**: AcroForm-only forms (IMM5669); forms where Pathway 2 bootstrap is too complex or the template binding is nonstandard.

**Files involved**:
- `pdf/pdf-fill-xfa/src/filler.rs::emit_setvalue_ops` — builds the `Vec<(String, Value)>` ops list from form spec + payload
- `pdf/pdf-fill-xfa/src/lib.rs::fill_5669` — form-specific entrypoint; calls `emit_setvalue_ops` then `fill_with_bun`
- `pdf/pdf-fill-xfa/src/lib.rs::fill_with_bun(source_bytes, ops)` — spawns Bun subprocess with ops JSON on stdin; pdfjs applies each via `annotationStorage.setValue`, saves, returns modified bytes on stdout

**Pitfalls**:
- Empty ops list + `saveDocument` → pdfjs crash: "null is not an object (evaluating 'storage.get')" — skip Bun pass entirely when ops is empty and return source bytes unchanged (Trap 8)
- Bun JIT OOM on ARM64 — must set `BUN_JSC_useJIT=false` env before spawning (Trap 7)

---

## The 11 Traps (every one we hit)

### Trap 1 — Flat vs nested payload contract drift

**Symptom**: fields in the filled PDF are blank; no error thrown. Happens in Pathway 1 and 3 when the fill entrypoint (e.g. `fill_imm0008`) receives a nested JSON object but `apply_main_payload` / `fill_pdf` expects flat dot-notation keys.

**Root cause**: callers (CLI, desktop app, unit tests) may pass `{"personal": {"family_name": "Zhang"}}` while sections do `.get("personal.family_name")`.

**Fix**: add a `flatten_if_nested(value: &Value) -> Value` helper that detects if the top-level keys contain no dots AND values are objects, then recursively flattens to dot-notation. Call it at the boundary in each `fill_<form>` entrypoint before delegating to sections.

---

### Trap 2 — LOV code coercion (CRITICAL)

**Symptom**: dropdown/radio fields appear filled in Acrobat but show wrong selection; parity gate shows DIVERGENT.

**Root cause**: IRCC XFA forms bind widgets to numeric codes, NOT human-readable labels. The parity oracle reads back codes from the PDF. If you write a label ("Married") instead of the code ("02"), parity fails.

**Known code mappings** (partial — always verify from `lov_source/<form>.json`):
| Form | Field | Code |
|------|-------|------|
| IMM0008 | Marital status = Married | `02` |
| IMM5257 | Purpose = Tourism | `2` |
| IMM5257 | Program = PNP | `13` |
| IMM5406 | Marital status = Married | `5` (different from 0008!) |

Single-letter codes (`T` for Tourism, `B` for Business) are WRONG for forms using numeric codes. Re-check after every IRCC form update — codes can change between versions.

**Fix**: always look up via `lov_source/<form>.json` in `lov_lookup.rs`. Never hardcode label strings into XFA path values.

---

### Trap 3 — Date format split

**Symptom**: date fields blank or partially filled (only year, or only month).

**Root cause**: XFA date fields are split across three separate leaf nodes — `DOBYYYY`, `DOBMM`, `DOBDD` (or equivalent). Payload arrives as a single ISO string `"1990/06/15"` or `"1990-06-15"`.

**Fix**: `_date_parts(s: &str) -> (String, String, String)` helper that accepts both `YYYY-MM-DD` and `YYYY/MM/DD` and returns `(year, month, day)` as zero-padded strings. Wire into the field type handler for `type = "date"` in `fill_pdf`.

---

### Trap 4 — AcroForm /V materialization (and when NOT to normalize)

**Symptom**: form looks correct in Adobe Acrobat; macOS Preview shows entirely blank fields.

**Root cause**: Rust filler writes XFA datasets XML. Adobe Acrobat resolves field values via XFA binding at render time — XFA-aware. macOS Preview is NOT XFA-aware; it only reads AcroForm widget `/V` entries. Rust filler does not write `/V`.

**NOT a bug for XFA-only forms.** The following 12 forms are XFA-datasets-only and will always look blank in Preview — this is expected, correct behavior:
- 5404, 5562 (XFA-packet-inject)
- 0104, 1294, 1295, 1344, 5257, 5532, 5645, 5708, 5709, 5710 (datasets-rewrite)

**Why normalize cannot be added to these forms**: adding `fill_with_bun_normalize` after the Rust fill would cause pdfjs `saveDocument()` to re-serialize the XFA tree. Forms with row-replication logic (5645, 1344, etc.) or no AcroForm widget bindings have their XFA datasets corrupted or silently dropped by pdfjs — breaking the pypdf parity gate.

**Forms where normalize IS applied** (have AcroForm /V widget bindings that pdfjs can handle):
- 0008: `fill_with_bun_normalize` runs after template-mutation
- 5476, 5669: Bun setvalue path writes `/V` directly via `saveDocument()`

**Verification for XFA-only forms**: use pypdf parity (see SOP below), NOT visual inspection in Preview. Adobe Acrobat is the authoritative visual viewer for these forms.

---

### Trap 5 — `saveDocument` strips unbound flat XML

**Symptom**: Bun pass runs without error; Acrobat shows blank fields even though Rust wrote datasets XML.

**Root cause**: if the datasets XML element paths don't match the template's binding expressions, pdfjs `saveDocument()` treats them as orphan/unknown nodes and discards them during XFA normalization.

**Fix**: always emit deep-nested XPath strings that match the template binding. Pathway 1 achieves this via DOM mutation of the pristine template. Pathway 3 achieves this by reading the existing datasets stream (which already has correct structure) and mutating leaf text only. Do NOT construct flat `<fieldName>value</fieldName>` elements — they will be discarded.

---

### Trap 6 — orig_oid_<form>.json must match ENCRYPTED PDF OID

**Symptom**: incremental update produces a PDF that Acrobat rejects with "file has been altered" or datasets are not applied.

**Root cause**: `extract-orig-oid` must read the OID from the ENCRYPTED source PDF. If you run it on a qpdf-flattened (decrypted) copy, object numbers shift and the recorded OID doesn't match the actual object in the encrypted PDF's cross-reference table.

**Fix**: `extract-orig-oid` handles this correctly — always pass it the original encrypted PDF path, not a temp flattened copy.

---

### Trap 7 — Bun JIT OOM on ARM64

**Symptom**: `fill_with_bun` subprocess exits with "Ran out of executable memory" or signal 6 (SIGABRT) on Apple Silicon.

**Root cause**: Bun's JSC JIT allocates a large executable memory arena upfront on ARM64; on machines with tight address space or RLIMIT_DATA constraints, this OOMs.

**Fix**: set `BUN_JSC_useJIT=false` in the environment before spawning the Bun process:
```rust
std::process::Command::new("bun")
    .env("BUN_JSC_useJIT", "false")
    // ...
```
This disables JIT and runs the pdfjs bundle interpreted. Performance is acceptable for single-form operations.

---

### Trap 8 — Empty annotationStorage + saveDocument crashes pdfjs

**Symptom**: `fill_with_bun` exits non-zero with "null is not an object (evaluating 'storage.get')" in stderr.

**Root cause**: pdfjs `annotationStorage.setValue` initializes internal state lazily. If `saveDocument()` is called with zero prior `setValue` calls and the document has annotation widgets, the storage is null.

**Fix**: in `fill_with_bun`, check if the ops list is empty before spawning. If empty, return the source bytes unchanged:
```rust
if ops.is_empty() {
    return Ok(source_bytes.to_vec());
}
```

---

### Trap 9 — pypdf parity oracle subset semantics

**Symptom**: parity gate fails claiming nodes are "missing" even though the plugin clearly writes them.

**Root cause**: early versions of `pypdf_parity.py` used strict equality (`backend.keys() == plugin.keys()`). This fails when the plugin writes additional metadata or structural nodes not present in the backend reference.

**Fix**: gate semantics are `backend.keys() ⊆ plugin.keys()` — the plugin is allowed to emit MORE nodes than the backend. The oracle also excludes LOV metadata, structural XFA nodes, and nodes listed in `EXCLUDED_TAGS`. Plugin writing extra nodes is fine; missing any backend node is a failure.

---

### Trap 10 — 5476 widget gap (Preview blank, Acrobat works)

**Symptom**: IMM5476 renders correctly in Acrobat; Preview shows blank. `fill_generic` was routing 5476 through the datasets-rewrite path instead of the setvalue+Bun path.

**Root cause**: 5476 has AcroForm widget bindings that pdfjs CAN correctly materialize without corrupting XFA structure (confirmed: 44/44 nodes, 0 missing in pypdf parity). Routing it through datasets-rewrite only (no /V write) meant Preview couldn't render it.

**Fix**: `fill_generic` now special-cases 5476 → `emit_setvalue_ops` + `fill_with_bun` (setvalue mode), same as 5669. pdfjs `saveDocument()` writes both AcroForm `/V` and re-serializes XFA datasets correctly.

**Critical distinction**: this fix applies ONLY to forms where pdfjs can safely handle the XFA structure. Do NOT apply this pattern to forms with row-replication (5645, 1344, etc.) — pdfjs will corrupt those datasets. Verify with pypdf parity after ANY change to the `fill_generic` routing table.

---

### Trap 11 — Encrypted PDF mutation

**Symptom**: lopdf write fails with "cannot modify encrypted stream" or produces a corrupt PDF.

**Root cause**: V4/R4 AES-128 encryption (all modern IRCC PDFs) prevents direct stream mutation by lopdf. The two legal mutation patterns are:

**Pattern A — Incremental-preserve-sig** (Pathways 1, 3):
- Read encrypted PDF bytes
- Parse datasets stream OID from `orig_oid_<form>.json`
- Build new compressed stream content (mutated XML)
- Append as incremental update (new xref + trailer pointing to updated OID)
- Cryptographic sig becomes "Protected B" (acceptable for IRCC use)
- Implemented in `incremental.rs::fill_pdf_incremental_with_datasets`

**Pattern B — qpdf-flatten then mutate** (Pathways 2, 4):
- Shell out to `qpdf --decrypt` to produce a flat decrypted copy
- Mutate the flat copy freely with lopdf
- Original sig is broken (G6 OOS) — acceptable when the form is unsigned or sig is already invalid
- Used in `inject_xfa_packet` for IMM5406

---

### Trap 12 — pypdf oracle scripts not migrated from formbro

**Symptom**: `pypdf_parity_acroform.py` exists only as a `.pyc` cache in `formbro/tools/pdf-fill-xfa/tests/fixtures/__pycache__/` — the `.py` source was removed when plugin tools were extracted to `formbro-plugin` (commit `5b194c18` in formbro). Plugin fixtures only had `pypdf_parity.py`, missing the AcroForm widget oracle.

**Root cause**: commit `9a22155e` in formbro added `pypdf_parity_acroform.py`, then `5b194c18` deleted the entire `tools/` dir without migrating the new script.

**Fix**: recovered via `git show 9a22155e:tools/pdf-fill-xfa/tests/fixtures/pypdf_parity_acroform.py` and copied to `pdf/pdf-fill-xfa/tests/fixtures/pypdf_parity_acroform.py`.

**Rule**: whenever deleting a directory that contains test oracle scripts, explicitly grep for `*.py` oracle files and migrate them before deletion.

---

### Trap 13 — opening PDFs for visual inspection

**Wrong**: `open filled.pdf` — uses macOS Preview, which cannot render XFA datasets. Form looks completely blank.

**Wrong**: `open -a "Preview" filled.pdf` — same result.

**Correct**:
```bash
# Visual inspection: always use Adobe Acrobat
open -a "Adobe Acrobat" filled.pdf

# Programmatic verification: always use pypdf
python3 pdf/pdf-fill-xfa/tests/fixtures/pypdf_parity.py plugin.pdf backend_reference.pdf
```

For XFA-only forms (the 12 that don't get normalize), **pypdf parity is the authoritative verification method**. Adobe Acrobat confirms visual rendering. Preview is never useful for verifying XFA form fill.

---

## Full 15-Form Verification SOP

Run this after ANY change to fill logic, form JSON, or XFA pathways:

```bash
cd pdf/pdf-fill-xfa

# Step 1: regenerate all test output PDFs
cargo test --release 2>&1 | tail -5
# expect: test result: ok. N passed; 0 failed

# Step 2: pypdf parity for all 11 generic forms
PY=tests/fixtures/pypdf_parity.py
OUT=target/test-output
FX=tests/fixtures
for form in 0104 1294 1295 1344 5257 5476 5532 5645 5708 5709 5710; do
  python3 $PY $OUT/parity-${form}-plugin.pdf $FX/${form}_backend_reference.pdf 2>&1 | \
    grep -E "missing|divergent" | awk -v f=$form '{print f": "$0}'
done

# Step 3: pypdf parity for bespoke forms
python3 $PY $OUT/imm0008-plugin.pdf $FX/imm0008_backend_reference.pdf 2>&1 | grep -E "missing|divergent" | awk '{print "0008: "$0}'
python3 $PY $OUT/imm5406-plugin.pdf $FX/imm5406_backend_reference.pdf 2>&1 | grep -E "missing|divergent" | awk '{print "5406: "$0}'
python3 $PY $OUT/imm5562-plugin.pdf $FX/imm5562_backend_reference.pdf 2>&1 | grep -E "missing|divergent" | awk '{print "5562: "$0}'
python3 $PY $OUT/v2-5669-rust.pdf   $FX/5669_backend_reference.pdf    2>&1 | grep -E "missing|divergent" | awk '{print "5669: "$0}'

# All lines should show: missing: 0  divergent: 0

# Step 4: visual spot-check in Adobe Acrobat
open -a "Adobe Acrobat" $OUT/imm0008-plugin.pdf $OUT/v2-5669-rust.pdf $OUT/parity-5476-plugin.pdf
# Verify fields are filled and readable
```

**Expected output for a healthy codebase** (verified 2026-05-12, v1.2.0):

| Form | backend nodes | plugin nodes | missing | divergent |
|------|:---:|:---:|:---:|:---:|
| 0008 | 103 | 103 | 0 | 0 |
| 5406 | 41 | 41 | 0 | 0 |
| 5562 | 15 | 15 | 0 | 0 |
| 5669 | 108 | 108 | 0 | 0 |
| 0104 | 7 | 11 | 0 | 0 |
| 1294 | 121 | 122 | 0 | 0 |
| 1295 | 119 | 120 | 0 | 0 |
| 1344 | 136 | 138 | 0 | 0 |
| 5257 | 113 | 114 | 0 | 0 |
| 5476 | 44 | 44 | 0 | 0 |
| 5532 | 57 | 57 | 0 | 0 |
| 5645 | 55 | 55 | 0 | 0 |
| 5708 | 119 | 120 | 0 | 0 |
| 5709 | 130 | 131 | 0 | 0 |
| 5710 | 117 | 118 | 0 | 0 |

---

## Pre-commit Checklist

Before merging ANY form change (new form, update, or fix):

1. `cargo test --release --test <form>_pypdf_parity` → GREEN (0 missing / 0 divergent)
2. `./scripts/verify-all.sh` → no regression in any other form
3. Open showcase PDF in Adobe Acrobat — all fields render with correct values
4. Open in macOS Preview — AcroForm /V fields render correctly (applies to any form with AcroForm widgets; pure XFA forms render only in Acrobat and that is expected)
5. Acrobat sig status: "Protected B" is acceptable; "INVALID — out of scope" (G6 OOS) is acceptable for Pathway B forms; "INVALID — content modified" for Pathway A forms is a regression

---

## Anti-Patterns (do not do these)

- **Do not** reach into `~/formbro/backend/vendor/pdffiller/` from plugin code. Use `pdf/vendor/`. The two copies diverge independently; plugin code must only depend on `pdf/vendor/`.
- **Do not** skip the Bun normalize pass for any form except when ops is provably empty (Trap 8). Always materialize `/V` widgets.
- **Do not** use macOS Preview to verify XFA PDF fill. Preview cannot render XFA datasets — forms always look blank regardless of whether data is present. Use pypdf parity for programmatic verification; use Adobe Acrobat (`open -a "Adobe Acrobat" file.pdf`) for visual spot-check.
- **Do not** add `fill_with_bun_normalize` to generic XFA forms (0104, 1294, 1295, 1344, 5257, 5532, 5645, 5708, 5709, 5710) — pdfjs will corrupt row-replication datasets and break pypdf parity. These forms are XFA-only by design; Adobe Acrobat renders them correctly.
- **Do not** manually edit `form_<id>.json` LOV code values. Regenerate via `extract-dataset-paths` and re-verify; manual edits drift on next IRCC form update.
- **Do not** push to main without running `./scripts/verify-all.sh`. A fix for one form must not break the other 14.
- **Do not** use `Object::Name(b"datasets")` when constructing XFA packet array entries — use `Object::String(b"datasets", StringFormat::Literal)` (Pathway 2 specific).
- **Do not** pass a qpdf-flattened PDF to `extract-orig-oid` — always pass the encrypted original (Trap 6).
