---
name: anypdf-fill
description: Use when a user wants to identify, validate, or fill a supported PDF form through AnyPDF.
---

# AnyPDF Fill

## Contract

- In an installed plugin, read `runtime-manifest.json` at the plugin root and
  run the current platform's `anypdf` path. Do not assume it is on `PATH`.
- The host AI extracts user facts using the customer's own AI environment and cost.
- AnyPDF does not call paid model APIs.
- Do not put `ANYPDF_TOKEN` or raw backend tokens into prompts, logs, or files.
- Do not upload original source documents to AnyPDF by default.
- If the user names a form in natural language, resolve it first and use the returned `form_id`.
- Fetch the form schema first, transform user facts into that schema, validate, then fill.
- Validation errors stop compile/fill. Missing-data warnings return
  `confirmation_required`; continue only after explicit user confirmation, or
  ask for the missing information when the user rejects the incomplete fill.
- Treat `barcode_status` as an explicit two-state submission gate; never infer it
  from the form name or PDF appearance.

## Commands

```bash
anypdf inspect --pdf /absolute/path/to/form.pdf --json
anypdf forms resolve --query "BC PNP employer declaration" --json
anypdf forms schema --form-id IMM5257 --json
anypdf validate --form-id IMM5257 --input applicant.json --json
anypdf fill --form-id IMM5257 --input applicant.json --output ./out --json
```

Use `inspect` for an unknown local PDF before any backend workflow. It is
read-only, does not upload the PDF, and returns classification, a stable schema
fingerprint, field descriptors, permissions, and `unlabeled_fields`. When
`unlabeled_fields` is non-empty, use the host's visual tools on the reported
page and rectangle only; do not send the PDF to a model API or invent labels.
Fields marked `computed` are excluded from the generated input schema.

If `fill` returns `phase=confirmation_required` (action exit code 11), show the
warnings and missing fields to the user. Continue only after an explicit yes:

```bash
anypdf fill --form-id IMM5257 --input applicant.json --output ./out \
  --allow-incomplete \
  --validation-id <validation_id> \
  --warnings-hash <warnings_hash> \
  --data-hash <data_hash> \
  --json
```

If the user declines, do not compile or fill. Return the typed collect-more-data
action with:

```bash
anypdf fill --form-id IMM5257 --input applicant.json --output ./out \
  --reject-incomplete --json
```

Do not invent or edit validation ids or hashes. Validation-error actions use
exit code 10 and require corrected input.

## Callback recovery

The CLI persists a fill-result callback receipt before contacting the backend.
If a fill reports a pending callback, do not rerun the filler. Retry only the
recorded callback:

```bash
anypdf fill-result retry --receipt <path> --json
```

Exact retry is safe. The receipt is removed only after the CLI validates the
backend acknowledgment; otherwise keep it and retry the same command later.

## Environment

Production fills automatically request a short-lived signed template download,
verify its SHA-256, and store it in an OS-private cache. `ANYPDF_CACHE_ROOT` is
an optional absolute cache override for managed environments. `--output` is a
directory and must remain outside the protected template cache.

`ANYPDF_TEMPLATE_ROOT` is an optional development override. When set, it must
be an absolute `<vendor_root>/pdfs` directory. Relative paths and directories
not named `pdfs` are rejected.

Do not derive a template filename from the public `form_id`. The schema and
compile responses provide the backend-approved `template_stem`; the CLI checks
that both responses agree and selects `<template_stem>.pdf` from the configured
root. Examples of approved mappings include:

- IMM form: `IMM5257` -> `5257.pdf`
- Generic form: `BCPNP_EMPLOYER_DECLARATION` -> `bcpnp_employer_declaration.pdf`

Local cache and output structure is checked before compile. Stable errors
include `template_root_invalid`, `template_download_invalid`,
`template_cache_invalid`, and `output_template_root_conflict`.
`ANYPDF_FILL_BIN` may optionally point to a specific `anypdf-fill` executable;
normally the packaged sibling binary is discovered.

The public package carries `config/backend-verification.json`, whose Ed25519
public key and key id are bound to `manifest.json`. The filler loads that
configuration automatically; do not configure a development HMAC key in a
distributed plugin.

## Barcode submission gate

Inspect `barcode_status` in the fill JSON:

- `user_action_required`: tell the user that the form contains 2D PDF417
  barcodes used by IRCC intake. The filled PDF must be opened in Adobe Acrobat
  Reader and its Validate button clicked before submission so the barcodes are
  regenerated. Do not submit the PDF before this step is complete.
- `not_applicable`: do not show the Acrobat Validate instruction.

Missing, null, or unknown `barcode_status` is a contract error, not
`not_applicable`.

### Token Setup

Use `ANYPDF_TOKEN` from the host environment or run:

```bash
anypdf auth login --token-env ANYPDF_TOKEN
```

`auth login` stores the token in the OS credential store. It must not write a plaintext token file.
