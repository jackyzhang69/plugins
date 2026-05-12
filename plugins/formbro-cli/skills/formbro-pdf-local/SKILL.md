---
name: formbro-pdf-local
description: 100% local IRCC PDF form filling (no backend). 4 fill pathways for XFA/AcroForm PDFs. Triggers (中): PDF 填表, IRCC 表格, plugin PDF. Triggers (EN): IRCC PDF fill, plugin PDF, local PDF form, XFA, AcroForm.
---

# formbro-pdf-local — Skill

Plugin for 100% local IRCC PDF form filling. Works for desktop/CLI users without
sending data to backend. Architecture: Rust (`pdf-fill-xfa`) + Bun (`pdfjs`) +
vendored IRCC PDFs.

## Quick reference

**4 fill pathways**:

| Pathway | When to use | Code path |
|---|---|---|
| 1. Template-mutation | Bespoke deep XFA forms (IMM0008) | `pdf-fill-xfa/src/bespoke/<form>/{template_mutation,apply}.rs` |
| 2. XFA-packet-inject | Bespoke without datasets stream (IMM5406) | `pdf-fill-xfa/src/incremental.rs::inject_xfa_packet` |
| 3. Datasets-rewrite | 12 generic forms (filler.rs) | `pdf-fill-xfa/src/filler.rs::fill_pdf` |
| 4. Setvalue+Bun | AcroForm-only or no datasets (IMM5669) | `pdf-fill-xfa/src/filler.rs::emit_setvalue_ops` + `lib.rs::fill_with_bun` |

**Universal end step**: `fill_with_bun_normalize` runs pdfjs `saveDocument()` to materialize datasets values into AcroForm `/V` widgets. Without this, macOS Preview shows blank fields (Adobe Acrobat renders XFA-aware, Preview does not).

## Decision tree for new forms

```
Source PDF has datasets stream in /XFA array?
├── YES
│   ├── Form is in `MAIN_FIELD_PATHS`-style deep template? → Template-mutation (Pathway 1)
│   └── Otherwise → Datasets-rewrite (Pathway 3)
└── NO
    ├── Can synthesize datasets from form spec? → XFA-packet-inject (Pathway 2)
    └── AcroForm-only widgets → Setvalue+Bun (Pathway 4)
```

## pypdf parity gate workflow

Every form has a parity gate: `tests/<form>_pypdf_parity.rs`.

```bash
cargo test --release --test <form>_pypdf_parity
# expect: 0 missing / 0 divergent
```

Oracle (Python): `pdf/pdf-fill-xfa/tests/fixtures/pypdf_parity.py`.
- Extracts user-data nodes from BOTH plugin output and backend reference PDF
- Asserts subset semantics: `backend.keys() ⊆ plugin.keys()` AND values match

## Read next (deep dives)

- `references/add-new-form.md` — onboarding a new IRCC PDF
- `references/update-existing-form.md` — IRCC ships new form version
- `references/generate-test-data.md` — auto-synthesize demo payload + pypdf verify
- `references/lessons-learned.md` — 4 pathway details + every trap

## Quick commands

```bash
# Verify all forms still green
./scripts/verify-all.sh

# Add a new form
./scripts/add-form.sh <form_id>

# Generate test data for a form
cargo run --release --bin generate_test_data -- --form <form_id>

# Run plugin for a single form
cargo run --release --bin pdf-fill-xfa -- --form <form_id> --input payload.json --output filled.pdf
```
