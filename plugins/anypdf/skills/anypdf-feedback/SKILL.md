---
name: anypdf-feedback
description: Use when a user reports an AnyPDF plugin, CLI, fill, validation, or form-specific problem.
---

# AnyPDF Feedback

In an installed plugin, read `runtime-manifest.json` at the plugin root and run
the current platform's `anypdf` path. Do not assume it is on `PATH`.

Create a small JSON report, then submit it with:

`anypdf feedback submit --report /absolute/report.json --json`

Include `idempotency_key`, `summary`, `description`, `reproduction_steps`, and
stable context such as `error_code`, `phase`, `form_id`, `plugin_version`,
`platform`, and `architecture`. Set `consent_diagnostics` explicitly.
Diagnostics are optional and should contain only minimal structured facts such
as exit code. Never include tokens, secrets, source documents, filled PDFs,
identity data, raw logs, or environment dumps.

Check progress with:

`anypdf feedback status --report-id ID --json`

Duplicate reports are linked to the original issue and do not create another
repair job. Report the returned status without promising a release date.
