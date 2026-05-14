---
name: formbro-pdf-local
description: "100% local IRCC PDF form filling (no backend). 4 fill pathways for XFA/AcroForm PDFs. Trigger phrases — Chinese: PDF 填表, IRCC 表格, plugin PDF. English: IRCC PDF fill, plugin PDF, local PDF form, XFA, AcroForm."
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

**Preview visibility by pathway**:

| Forms | Pathway | Preview visible? | Why |
|---|---|---|---|
| 0008 | Template-mutation + normalize | ✅ Yes | normalize pass writes AcroForm /V |
| 5406, 5562 | XFA-packet-inject | ❌ No (data in XFA datasets only) | No AcroForm /V bindings; Preview can't render XFA |
| 0104, 1294, 1295, 1344, 5257, 5532, 5645, 5708, 5709, 5710 | Datasets-rewrite | ❌ No (data in XFA datasets only) | Row-replication or missing bindings; adding normalize would corrupt XFA |
| 5476 | Setvalue+Bun | ✅ Yes | pdfjs setValue writes AcroForm /V |
| 5669 | Setvalue+Bun | ✅ Yes | AcroForm-only form |

**XFA-only forms look blank in Preview — this is expected.** Data IS written correctly; use pypdf to verify (see below). Only Adobe Acrobat renders XFA datasets.

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

**Two oracles** — pick by form type:

| Script | When to use | What it reads |
|---|---|---|
| `pypdf_parity.py` | XFA forms (all except pure AcroForm) | XFA `/AcroForm/XFA[datasets]` XML stream |
| `pypdf_parity_acroform.py` | AcroForm-only forms (currently none — 5669 gets a datasets stream via pdfjs) | `/AcroForm` widget `/V` entries |

**How to manually verify a filled PDF with pypdf:**

```bash
# XFA forms — compare plugin output against backend reference
python3 pdf/pdf-fill-xfa/tests/fixtures/pypdf_parity.py \
  target/test-output/parity-<form>-plugin.pdf \
  tests/fixtures/<form>_backend_reference.pdf
# stderr: "backend nodes: N  plugin nodes: M  missing: 0  divergent: 0"

# AcroForm forms — same interface, different oracle
python3 pdf/pdf-fill-xfa/tests/fixtures/pypdf_parity_acroform.py \
  target/test-output/<plugin>.pdf \
  tests/fixtures/<form>_backend_reference.pdf
# stderr: "backend widgets: N  plugin widgets: M  missing: 0  divergent: 0"

# Inspect raw XFA datasets XML from any filled PDF
python3 - <<'EOF'
from pypdf import PdfReader
from pathlib import Path
import sys, xml.dom.minidom

reader = PdfReader(sys.argv[1])
xml_bytes = reader.trailer["/Root"]["/AcroForm"]["/XFA"]
for i in range(len(xml_bytes)):
    if str(xml_bytes[i]) == "datasets":
        data = xml_bytes[i+1].get_object().get_data()
        print(xml.dom.minidom.parseString(data).toprettyxml(indent="  "))
        break
EOF filled.pdf

# Inspect AcroForm /V widget values
python3 - <<'EOF'
from pypdf import PdfReader
import sys
reader = PdfReader(sys.argv[1])
fields = reader.get_fields() or {}
for name, obj in sorted(fields.items()):
    v = obj.get("/V")
    if v: print(f"{name} = {v!r}")
EOF filled.pdf
```

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
