# Updating an Existing IRCC Form

When IRCC publishes a new version of an existing form, follow this workflow.

## Step 0 — Inputs

The new PDF from IRCC. Filename pattern: `<form_id>.pdf` (same name, different content).

## Step 1 — Replace source PDF in vendor

```bash
cp <new>.pdf ~/formbro-plugin/pdf/vendor/pdfs/<form_id>.pdf
# Also update formbro's copy (backend uses it)
cp <new>.pdf ~/formbro/backend/vendor/pdffiller/pdfs/<form_id>.pdf
```

## Step 2 — Re-extract orig_oid

The encrypted PDF's /XFA datasets OID may have shifted in the new version:

```bash
cd ~/formbro-plugin
cargo run --release --bin extract-orig-oid -- \
  pdf/vendor/pdfs/<form_id>.pdf \
  > pdf/vendor/orig_oid_<form_id>.json
```

## Step 3 — Audit form spec for new/renamed fields

Compare new PDF's XFA template to current `form_<form_id>.json`:

```bash
cargo run --release --bin extract-dataset-paths -- \
  --form <form_id> \
  --output /tmp/form_<form_id>_NEW.json
diff pdf/vendor/forms/form_<form_id>.json /tmp/form_<form_id>_NEW.json | head -100
```

Common changes:
- New field added → add to `form_<form_id>.json`, plus `dataset_paths_<id>.json` if needed
- Field renamed → update path tuple in `MAIN_FIELD_PATHS` or dataset_paths
- Field removed → remove from form_<form_id>.json (no harm leaving, but cleaner)
- LOV codes changed → re-extract LOV from new template

## Step 4 — Re-capture backend reference

```bash
# Use Step 7 invocation from add-new-form.md with the updated form_id
cd ~/formbro
python3 -c "
import sys; sys.path.insert(0, 'backend')
import json
from pathlib import Path
from services.pdf_filling.filler import fill_pdf
payload = json.loads(Path('~/formbro-plugin/pdf/pdf-fill-xfa/tests/fixtures/<form_id>_payload.json').expanduser().read_text())
bytes_out = fill_pdf('<form_id>', payload, Path('backend/vendor/pdffiller/pdfs/<form_id>.pdf'))
Path('~/formbro-plugin/pdf/pdf-fill-xfa/tests/fixtures/<form_id>_backend_reference.pdf').expanduser().write_bytes(bytes_out)
"
```

## Step 5 — Run parity gate

```bash
cargo test --release --test <form_id>_pypdf_parity -- --nocapture 2>&1 | tail -30
```

## Step 6 — Diagnose + fix

For each MISSING node: which old field is now at a new path? Update dispatch / `MAIN_FIELD_PATHS` / dataset_paths accordingly.

For each DIVERGENT node: LOV code changed or value transformation different. Audit normalize.rs handlers and `lov_source/<form>.json`.

## Step 7 — Regression check

After form is green again, re-run ALL gates to ensure no other form broke:

```bash
./scripts/verify-all.sh
```

## Step 8 — Bump version

`Cargo.toml` workspace.package.version: bump minor (form change is feature) or patch (bug fix from IRCC errata).

## Step 9 — Tag + publish

```bash
git tag -a v<new_version> -m "feat: update <form_id> to IRCC's new version"
git push origin main --tags
```

Publish workflow auto-pushes new release artifacts → formbro-cli's next release picks them up.

## Step 10 — Cross-repo sync

Manually update formbro's `backend/vendor/pdffiller/pdfs/<form_id>.pdf` (already done Step 1). Backend Python continues to use its own copy.
