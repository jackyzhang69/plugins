# Auto-Generate Test Data

The `generate_test_data` Rust binary synthesizes a demo payload covering every field defined in `form_<id>.json`.

## Usage

```bash
cargo run --release --bin generate_test_data -- \
  --form <form_id> \
  [--output <path>] \
  [--update-fixtures]
```

Default output: `pdf/pdf-fill-xfa/tests/fixtures/<form_id>_payload.json`

## What it does

1. Loads `pdf/vendor/forms/form_<form_id>.json`
2. Enumerates all `{key: {type, ...}}` entries
3. For each field, picks representative value per `type`:
   - `input` → `"Demo_<key>"`
   - `input_number` → `"42"`
   - `check` → `true`
   - `radio` / `radio_12` / `radio_21` / `radio_23` / `radio_31` → first option from `values`
   - `radio_check` → emits both checked + unchecked variants
   - `select` → first label from `values`
   - `select_city` → `{"province": "BC", "city": "Vancouver"}`
4. Writes payload to specified output
5. If `--update-fixtures`, also runs:
   - `fill_generic(form_id, payload)` → plugin PDF
   - Backend reference capture (requires `~/formbro` backend deps installed)
   - `pypdf_parity.py` gate
6. Exits 0 = GREEN; non-zero = stderr with diff

## When to regenerate

- IRCC ships new form version → regenerate after audit
- New field added → regenerate to cover
- Test fixtures look stale → regenerate

## Bulk regeneration

```bash
./scripts/verify-all.sh --regen
# Runs generate_test_data for every form + pypdf gate
```

## Manual override

For forms where auto-generated values aren't realistic (e.g., dates need real ISO format, country codes need specific values), edit `tests/fixtures/<form_id>_payload.json` directly after generation. Subsequent regenerations will warn before overwriting.
