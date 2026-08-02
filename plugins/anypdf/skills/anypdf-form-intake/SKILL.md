---
name: anypdf-form-intake
description: Submit an explicitly confirmed blank PDF as a new AnyPDF form request.
---

# New PDF request

Use this workflow only after the user explicitly confirms the selected PDF is
a blank form template and consents to upload it. The client checks `%PDF-`,
SHA-256, and a 50 MiB maximum before using a short-lived signed upload grant.

The server records that confirmation as an immutable exact-source fact. Safe retries with the same
source identity and bytes reuse it; a changed `content_sha256` requires a new confirmation. Do not
ask for a second blank-template confirmation during onboarding or recovery.

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
until status is `active`/`known_exact`. Intake owns provenance and queueing only.
For a PDF in an already-supported pathway, the admin pipeline must synthesize,
test, and certify missing form-specific assets rather than classify their absence
as an unsupported engine. During official-source binding, a non-identical
submitted blank is preserved and superseded by a new source-bound submission/run
using the issuing authority's latest official HTTPS PDF. `rejected` and `failed`
are terminal for that exact submission, not proof that the generic pathway is
unsupported; any retry requires a new governed source/run or a repaired capability.

Never remove `--confirm-blank`. Never upload a filled form, source evidence,
identity document, or another private document. The client never prints PDF
bytes or signed upload tokens.
