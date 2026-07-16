---
name: anypdf-form-intake
description: Submit an explicitly confirmed blank PDF as a new AnyPDF form request.
---

# New PDF request

Use this workflow only after the user explicitly confirms the selected PDF is
a blank form template and consents to upload it. The client checks `%PDF-`,
SHA-256, and a 50 MiB maximum before using a short-lived signed upload grant.

```bash
anypdf intake submit --pdf /absolute/blank-form.pdf \
  --confirm-blank --idempotency-key <stable-key>
```

Keep the same idempotency key for a safe retry. If the server returns
`awaiting_upload`, the client uploads the declared file and finalizes it. Then
inspect one status response at a time:

```bash
anypdf intake status --submission-id <submission_id>
```

`known_exact` means an existing registered form can be used. `queued` or
`needs_review` means the server has not activated support; never promise a fill
until status is `active`/`known_exact`. Treat `rejected` and `failed` as terminal
non-fillable states.

Never remove `--confirm-blank`. Never upload a filled form, source evidence,
identity document, or another private document. The client never prints PDF
bytes or signed upload tokens.
