# AnyPDF Codex Plugin

AnyPDF is a server-owned PDF workflow client. The package contains public
skills, a platform-matched native `anypdf` client, its SHA-256 sidecar, and
documentation. It contains no PDF template, private mapping, admin tool, or
server credential.

Load `anypdf-capabilities` first. When the user asks what AnyPDF can do,
answer from the live CLI (`forms catalog`, `doctor`), not a remembered list.

The client uses `https://anypdf.jackyzhang.app` by default. Use the
`connect-anypdf` skill once to verify a credential supplied on stdin and save
only the mode-0600 canonical user slot at
`~/.jackyzhang.app/token/user.json`. The backend URL may be overridden with
`ANYPDF_BACKEND_URL` for development or self-hosting. Product requests use
only an in-memory short-lived exact-audience JWT. Neither value belongs in
plugin files, prompts, reports, logs, or command arguments. HTTP is accepted
only for loopback development URLs; all other backends must use HTTPS.

## Registered PDF fill

Use `anypdf-fill` to resolve a registered form, fetch its schema, validate the
user's data, submit one idempotent request, and retrieve the retained result.
The server owns templates, mappings, revision locking, and PDF execution.

When the user's source materials are Office or PDF, run `anypdf read --input
<absolute-path>` first. That command stays on this machine: it does not upload
the original file. Extracted text may be used by the host agent; fill JSON
still follows the existing fill path. Photos, screenshots, and scans are not
read this way — look at the original yourself.

## New PDF request

Use `anypdf-form-intake` only when the user provides an issuing-authority official blank PDF template
to register or request support. Never send a filled
form, identity document, or another private document. Send exactly one raw PDF
POST; the server computes SHA-256 and applies the 50 MiB/PDF input checks. The
command itself is the only confirmation; do not add a second interactive
blank-template confirmation. Reuse the same stable idempotency key when
retrying the same file. The receipt is final: there is no signed upload,
finalize, status/poll, job, run, or watcher request.

The receipt result is exactly one of:

- `known_exact`: an existing published revision matches the PDF; continue the
  registered form flow.
- `new_source`: record the returned `source_sha256`; it cannot be filled yet.
  Follow-up capability building is handled by the shared AnyPDF agents.
- `rejected`: the input is rejected and no source is accepted.

HTTP 200 and 201 can each return any of these results; do not infer the result
from the HTTP status.

If the wrong file was sent, use `tell-jacky` to request deletion and include the
exact returned `source_sha256`。不要在删除请求中附带原文件或其他无关内容。

## Tell Jacky

Use `tell-jacky` to draft a small product feedback report, show it to the user,
and submit it only after explicit confirmation. Diagnostics are opt-in and must
contain stable facts only; never include tokens, secrets, raw logs, or PDF bytes.

The client is non-interactive. Every command except `read` is JSON-first;
`read` prints Markdown. See each skill for the exact workflow and command
examples.
