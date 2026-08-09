---
name: anypdf-form-intake
description: Submit a PDF template as a new AnyPDF form request.
---

# New PDF request

Use this workflow when the user provides a PDF form template to register or
request support. The client checks `%PDF-`, SHA-256, and a 50 MiB maximum before
using a short-lived signed upload grant. Do not ask the user to re-confirm that
the file is blank; the submitted PDF is the template input.

```bash
anypdf intake submit --pdf /absolute/form.pdf \
  --idempotency-key <stable-key>
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
submitted template is preserved and superseded by a new source-bound submission/run
using the issuing authority's latest official HTTPS PDF. `rejected` and `failed`
are terminal for that exact submission, not proof that the generic pathway is
unsupported; any retry requires a new governed source/run or a repaired capability.

Never upload source evidence, identity documents, or other private non-template
documents through this path. The client never prints PDF bytes or signed upload
tokens.
