# New PDF request

## Talk to the human

Explain only whether the official blank template was recognized, needs support,
or was rejected, and what the person can do next. Keep command details and JSON
between tools. Never show credentials, source paths, or private document facts.

Use this workflow only when the user provides an issuing-authority official blank PDF template to
register or request support. Do not submit a filled form,
an identity document, or another private document. Before sending, the client
locally checks the `%PDF-` header, the 50 MiB maximum, and the SHA-256 hash. It
then sends exactly one raw PDF POST to the intake endpoint. The command itself
is the only confirmation; do not add a second interactive blank-template
confirmation.

Run [connect](connect.md) first so the installed package repairs the canonical
client slot. Set `ANYPDF` to that slot as instructed there; every agent command
below uses `$ANYPDF`, never whichever `anypdf` is first on PATH.

```bash
$ANYPDF intake submit --pdf /absolute/form.pdf \
  --idempotency-key <stable-key>
```

Use a stable idempotency key and reuse that same key when retrying the same
file. The command returns one JSON receipt; there is no signed upload,
finalize, status/poll, queue, job, run, or watcher step.

The receipt has exactly one of these results:

- `known_exact`: an existing published revision matches this PDF; continue the
  registered form flow.
- `new_source`: record the returned `source_sha256`; it cannot be filled yet.
  Follow-up capability building is handled by the shared AnyPDF agents.
- `rejected`: the input is rejected and no source is accepted.

HTTP 200 and 201 can each return any of these results; do not infer the result
from the HTTP status.

If the wrong file was sent, use `tell-jacky` to request deletion and include the
exact returned `source_sha256`。不要在删除请求中附带原文件或其他无关内容。

## Recovery before escalation

Keep typed recovery details between tools and explain the next choice plainly.
For a fill problem, use the bounded recovery procedure in [fill](fill.md)
before suggesting [tell-jacky](tell-jacky.md). Never send feedback automatically.
