# AnyPDF Claude Code Plugin

AnyPDF is a universal, server-owned PDF workflow client. The package contains
only public skills, a small Python 3.11 standard-library HTTPS client, and
documentation. It has no native executable, PDF template, private mapping, or
verification key.

The client uses `https://anypdf.jackyzhang.app` by default. Run the
`connect-anypdf` skill once to verify a token supplied on stdin and save it in
the mode-0600 local config at `~/.jackyzhang.app/token/jz.json`. The backend URL may be
overridden with `ANYPDF_BACKEND_URL`; `ANYPDF_TOKEN` remains an explicit,
ephemeral automation override. Neither value belongs in plugin files, prompts,
reports, logs, or command arguments. HTTP is accepted only for loopback
development URLs; all other backends must use HTTPS.

## Registered PDF fill

Use `anypdf-fill` to resolve a registered form, fetch its schema, validate the
user's data, submit one idempotent server job, and retrieve the retained result.
The server owns templates, mappings, revision locking, and PDF execution. A
status invocation performs exactly one GET; follow the server's `Retry-After`
value with a later status invocation until `succeeded` or `failed`.

## New PDF request

Use `anypdf-form-intake` only when the user has explicitly confirmed that a PDF
is a blank form template. The client checks the PDF header, size, and SHA-256,
then uses the server's signed upload and finalize flow. Never upload a filled
form, source evidence, identity document, or any other private document.

## Bug report

Use `anypdf-feedback` to submit a small redacted JSON report and inspect its
status. Diagnostics are opt-in and must contain stable facts only; never include
tokens, secrets, raw logs, PDF bytes, or identity data.

## Optional discovery preferences

`anypdf preferences get`, `set`, `clear`, and `catalog` support non-secret local
facets as a soft ranking hint. Preferences never filter or authorize a form.
Missing preferences preserve global behavior. If a resolve request returns
multiple candidates, show them and ask the user to choose a form/version; do not
guess.

The client is non-interactive and JSON-first. See each skill for the exact
workflow and command examples.
