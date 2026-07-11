---
name: anypdf-form-intake
description: Use when a user has a blank PDF form that may not yet be supported by AnyPDF.
---

# AnyPDF Form Intake

In an installed plugin, read `runtime-manifest.json` at the plugin root and run
the current platform's `anypdf` path. Do not assume it is on `PATH`.

Inspect and submit only a blank form template. Do not submit a completed or
partially filled PDF, source evidence, or identity document.

## Workflow

1. Confirm the PDF is blank and the user consents to upload it.
2. Run `anypdf intake submit --pdf /absolute/form.pdf --confirm-blank --json`.
3. If status is `known`, follow the returned fill command; do not create a new registration.
4. If status is `queued`, report the `submission_id` and check later with
   `anypdf intake status --submission-id ID --json`.
5. Treat `rejected`, `failed`, or `needs_review` as non-fillable states. Never
   promise support until status is `active`.

The CLI performs local inspection and hashing before upload. Keep output JSON
intact so submission ids and typed error codes remain actionable.

## Common mistakes

- Never remove `--confirm-blank` or infer consent.
- Never upload a filled PDF as a template.
- Never bypass a rejected validation or invent a form id.
