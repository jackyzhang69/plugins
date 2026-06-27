# Adding a New IRCC Form to the Plugin

Onboarding a new XFA or AcroForm PDF. Workflow takes 1-4 hours per form depending on complexity.

## Step 0 — Inputs

Required: source PDF from IRCC (`<form_id>.pdf`). Optional: existing `form_<id>.json` field spec from FormBro's backend vendor.

## Step 1 — Diagnose XFA structure

```bash
python3 <<'PY'
from pypdf import PdfReader
r = PdfReader("pdf/vendor/pdfs/<form_id>.pdf")
if r.is_encrypted: r.decrypt("")
xfa = r.trailer["/Root"]["/AcroForm"]["/XFA"]
xfa_list = list(xfa) if hasattr(xfa, "__iter__") else None
if xfa_list:
    packets = [str(xfa_list[i]) for i in range(0, len(xfa_list), 2)]
    print("XFA packets:", packets)
else:
    print("Single XFA stream")
PY
```

Output tells you which pathway:
- `datasets` packet present → Pathway 1 (template-mutation) OR Pathway 3 (datasets-rewrite)
- No `datasets` packet (just `preamble, config, template, ...`) → Pathway 2 (xfa_packet_inject) OR Pathway 4 (setvalue+Bun)

## Step 2 — Drop source PDF

```bash
cp <form_id>.pdf ~/formbro-plugin/pdf/vendor/pdfs/<form_id>.pdf
```

## Step 3 — Acquire form_<id>.json

If FormBro has it: `cp ~/formbro/backend/vendor/pdffiller/forms/form_<form_id>.json ~/formbro-plugin/pdf/vendor/forms/`.

If not: extract dataIds + types from XFA template:

```bash
cargo run --release --bin extract-dataset-paths -- \
  --form <form_id> \
  --output pdf/vendor/forms/form_<form_id>.json
```

Hand-audit the output: ensure every field has a `type` ("input", "input_number", "check", "radio", "select", "select_city", "radio_check", or compound radio variants).

## Step 4 — Generate orig_oid_<id>.json

```bash
cd ~/formbro-plugin
cargo run --release --bin extract-orig-oid -- \
  pdf/vendor/pdfs/<form_id>.pdf \
  > pdf/vendor/orig_oid_<form_id>.json
```

Verify the file contains `{"datasets_obj_id": <N>, ...}` with non-zero N.

## Step 5 — Add dataset_paths_<id>.json (Pathway 3 only)

For datasets-rewrite, you need a flat-dot-notation → XPath map. Use `extract-dataset-paths`:

```bash
cargo run --release --bin extract-dataset-paths -- \
  --form <form_id> \
  --emit-paths \
  --output pdf/vendor/forms/dataset_paths_<form_id>.json
```

For bespoke pathways (1, 2), define `MAIN_FIELD_PATHS` in `pdf/pdf-fill-xfa/src/bespoke/<form>/apply.rs`.

## Step 6 — Generate demo payload

```bash
cargo run --release --bin generate_test_data -- \
  --form <form_id> \
  --output pdf/pdf-fill-xfa/tests/fixtures/<form_id>_payload.json
```

## Step 7 — Capture backend reference PDF

Backend Python lives at `~/formbro/backend/services/pdf_filling/`. For a generic form (Pathway 3 or 4):

```bash
cd ~/formbro
python3 -c "
import sys; sys.path.insert(0, 'backend')
import json
from pathlib import Path
from services.pdf_filling.filler import fill_pdf  # or imm<id>_xfa for bespoke
payload = json.loads(Path('~/formbro-plugin/pdf/pdf-fill-xfa/tests/fixtures/<form_id>_payload.json').expanduser().read_text())
bytes_out = fill_pdf('<form_id>', payload, Path('backend/vendor/pdffiller/pdfs/<form_id>.pdf'))
Path('~/formbro-plugin/pdf/pdf-fill-xfa/tests/fixtures/<form_id>_backend_reference.pdf').expanduser().write_bytes(bytes_out)
"
```

## Step 8 — Wire into dispatch

For Pathway 3 (generic): edit `pdf/pdf-fill-xfa/src/lib.rs::fill_generic` to handle `"<form_id>"` (usually nothing to change if filler.rs paths are present).

For Pathway 4 (setvalue+Bun): edit `pdf/pdf-fill-xfa/src/lib.rs` to add `pub fn fill_<form_id>` (mirror `fill_5669`).

For Pathways 1+2 (bespoke): create `pdf/pdf-fill-xfa/src/bespoke/<form>/` with `mod.rs`, `apply.rs`, `template_mutation.rs` (if Pathway 1). Add `pub fn fill_<form>` in `lib.rs`.

## Step 9 — Add parity gate

```bash
cat > ~/formbro-plugin/pdf/pdf-fill-xfa/tests/<form_id>_pypdf_parity.rs <<'RUST_EOF'
//! <form_id> pypdf parity gate.
use std::path::PathBuf;
use std::process::Command;
#[test]
fn <form_id>_pypdf_parity_gate() {
    let m = PathBuf::from(env!("CARGO_MANIFEST_DIR"));
    let payload: serde_json::Value = serde_json::from_str(
        &std::fs::read_to_string(m.join("tests/fixtures/<form_id>_payload.json")).unwrap()
    ).unwrap();
    let bytes = pdf_fill_xfa::fill_<form_id>(payload).expect("fill failed");
    let out = m.join("target/test-output/<form_id>-plugin.pdf");
    std::fs::create_dir_all(out.parent().unwrap()).unwrap();
    std::fs::write(&out, &bytes).unwrap();
    let output = Command::new("python3")
        .arg(m.join("tests/fixtures/pypdf_parity.py"))
        .arg(&out)
        .arg(m.join("tests/fixtures/<form_id>_backend_reference.pdf"))
        .output().unwrap();
    eprintln!("{}", String::from_utf8_lossy(&output.stderr));
    assert!(output.status.success());
}
RUST_EOF
```

## Step 10 — Iterate until green

```bash
cargo test --release --test <form_id>_pypdf_parity -- --nocapture 2>&1 | tail -30
```

If RED with missing/divergent nodes:
1. Read the "FIRST 20 MISSING" stderr block
2. Diagnose per missing field — see `lessons-learned.md` for trap patterns
3. Fix in dispatch / builder / sections
4. Re-run, commit per fix

Repeat until 0 missing / 0 divergent.

## Step 11 — Acrobat manual sign-off

```bash
cargo test --release --test showcase_samples -- --nocapture
open -a "Adobe Acrobat" ~/Desktop/tests/showcase/v2-<form_id>-rust.pdf
```

Verify fields render visually. Also open in `Preview.app` to confirm AcroForm /V widgets work for non-XFA viewers.

## Step 12 — Commit per fix + final PR

One commit per logical fix. Final commit message: `feat(plugin): add <form_id> via <pathway>; pypdf gate green`.
