---
name: anypdf-feedback
description: Submit and inspect a redacted AnyPDF bug report.
---

# Bug report

Create a small JSON report with `summary`, `description`, and
`reproduction_steps`; include stable context such as `form_id`, `error_code`,
`phase`, `plugin_version`, and `platform` when known. Set
`consent_diagnostics` explicitly. Submit it with a stable idempotency key:

```bash
anypdf feedback submit --report /absolute/report.json \
  --idempotency-key <stable-key>
```

Inspect one status response at a time:

```bash
anypdf feedback status --report-id <report_id>
```

The server deduplicates matching reports. Report the returned status without
promising a repair or release date.

Diagnostics must contain only minimal structured facts and only when the user
consents. Never include tokens, secrets, raw logs, environment dumps, PDF
bytes, source evidence, filled documents, or identity data.
