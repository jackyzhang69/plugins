---
name: connect-anypdf
description: Connect the public AnyPDF client once using a canonical credential supplied only on stdin.
---

## Shared platform token (host agent — mandatory)

- Canonical durable user credential: `~/.jackyzhang.app/token/user.json` (`jz_` only; `credential_kind=user`, `slot=user`).
- **One Portal user token for the whole platform.** FormBro, AnyChat, AnyPDF, AnyWeb, and EasyBooks all use this same file. If it already exists from any official plugin, do **not** ask the human to log in again and do **not** say this product needs a different Portal token.
- **Consumption differs by product; the durable token does not.**
  - **Exchange mode** (`anychat`, `anypdf`, `anyweb`, EasyBooks/`eb`): CLI calls `POST /v1/token/exchange` with `aud=<product>` and uses a short-lived memory-only JWT on product routes. Raw `jz_` is not a product bearer.
  - **Introspect mode** (`formbro`): CLI/API sends raw `jz_`; FormBro backend calls accountd `POST /v1/api-tokens/introspect`. `aud=formbro` exchange is invalid (`unknown_audience`).
- Retired local prefixes (`fb_`, `ap_live_`, `eb_live_`, …) are not Portal credentials — never ask the human to paste them into a Portal plugin.
- On load, if `user.json` is missing but a legacy on-disk `jz_` exists (`token/jz.json` or migratable product-local fields), **migrate immediately** via this product CLI `whoami`/`doctor`/login path. User must feel no re-paste.
- Never print or log the raw token. Prefer masked doctor/whoami output.
- Do not create product-local durable token files. Plugin runtime stays under `~/.jackyzhang.app/<plugin_id>/` only.


## Token delivery to the host agent (connect) — LOCKED 2026-08-14

The host agent performs connect **for** the human. **Never** tell the human to open a terminal and run login commands themselves.

Accept input in this order:

1. **File containing the token (preferred).** If the human provides a filesystem path (e.g. `~/Desktop/jacky-token.txt`) or an attached/readable file whose contents are a single `jz_…` value (optional surrounding whitespace/newline only):
   - Read the file in the agent tool channel.
   - Pipe the token to the product CLI via stdin only: `login --token-stdin` (or the product's equivalent).
   - Do **not** put the token on argv, in chat echo, in logs, or in screenshots.
   - Confirm success with masked doctor/whoami only.

2. **Plaintext token in chat (allowed, discouraged).** If the human pastes a raw `jz_…` into the conversation:
   - **Warn once** in plain language: prefer a local file path next time so the secret is not retained in chat history; do **not** refuse.
   - Proceed immediately: feed that value to `login --token-stdin` via a non-echoing agent-side pipe (temp file mode 0600 deleted after use is OK; never `echo TOKEN |` in a way that lands in shell history if avoidable).
   - Do not repeat the raw token back in the reply.

3. **No token yet.** Ask the human to send either a **file path** (best) or paste the token. Still do not ask them to run terminal commands.

Hard rules:

- `--token <value>` / argv secrets remain **forbidden** for the CLI.
- Agent may read a user-supplied path and stdin-feed the CLI; that is the supported file path.
- After any successful connect, other plugins must not re-prompt when `user.json` is present.


# Connect AnyPDF

The public client defaults to `https://anypdf.jackyzhang.app`. To use another
backend for local development, set `ANYPDF_BACKEND_URL` for this invocation;
the URL is resolved each time and is never written into the credential slot.

Run for the user this in their own terminal and enter the credential at the
non-echoing prompt (the value is not a command-line argument or agent input):

```bash
anypdf login --json
```

Do not ask for the credential in chat or put it in an agent tool call, pipe,
argument, shell history, prompt text, report, log, or output. The launcher reads
stdin, verifies it through accountd, and atomically writes only the mode-0600
canonical user slot `~/.jackyzhang.app/token/user.json` inside a mode-0700
directory. A failed verification never writes a slot.

Subsequent commands exchange the user slot for a short-lived exact-audience JWT
held only in process memory; the durable value is never sent to the AnyPDF
product API:

```bash
anypdf whoami --json
anypdf doctor --json
anypdf forms catalog
```

`whoami` returns only public token metadata (id, type, scopes, form allowlist,
status, and expiry), never the raw token or owner secrets. `doctor` checks local
backend resolution and credential safety without making an unsolicited remote
request. `logout` removes only the saved user slot. The native client has no
environment credential override.
