# AnyPDF Codex Plugin

AnyPDF is a server-owned PDF workflow client. The package contains public
skills, a platform-matched native `anypdf` client, its SHA-256 sidecar, and
documentation. It contains no PDF template, private mapping, admin tool, or
server credential.

The client uses `https://anypdf.jackyzhang.app` by default. Run the
`connect-anypdf` skill once to verify a credential supplied on stdin and save
only the mode-0600 canonical user slot at
`~/.jackyzhang.app/token/user.json`. The backend URL may be overridden with
`ANYPDF_BACKEND_URL` for development or self-hosting. Product requests use
only an in-memory short-lived exact-audience JWT. Neither value belongs in
plugin files, prompts, reports, logs, or command arguments. HTTP is accepted
only for loopback development URLs; all other backends must use HTTPS.

## Registered PDF fill

Use `anypdf-fill` to resolve a registered form, fetch its schema, validate the
user's data, submit one idempotent server job, and retrieve the retained result.
The server owns templates, mappings, revision locking, and PDF execution. A
status invocation performs exactly one GET; follow the server's `Retry-After`
value with a later status invocation until `succeeded` or `failed`.

## New PDF request

Use `anypdf-form-intake` when the user provides a PDF form template to register
or request support. Do not ask them to re-confirm that it is blank. The client
checks the PDF header, size, and SHA-256, then uses the server's signed upload
and finalize flow. Never upload source evidence, identity documents, or other
private non-template documents through this path.

## Bug report

Use `anypdf-feedback` to submit a small redacted JSON report and inspect its
status. Diagnostics are opt-in and must contain stable facts only; never include
tokens, secrets, raw logs, PDF bytes, or identity data.

The client is non-interactive and JSON-first. See each skill for the exact
workflow and command examples.
