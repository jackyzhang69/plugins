---
name: formbro-fill
description: Agent-facing entrypoint for filling IRCC IMM PDF forms (IMM0008 / IMM5257 / IMM5645 / IMM5709 / etc.). Use this skill for ANY user intent that mentions "fill PDF / IMM form / generate the IMM5257 / give me the filled PDF". Auto-detects TR vs PR from the application; rejects LMIA (which is webform-only). The backend renders the PDFs and the CLI saves them locally.
when_to_use: |-
  Trigger phrases (PDF intent, NOT webform):
    - "fill IMM0008 / IMM5257 / IMM5710 / IMM5406 / etc."
    - "give me the filled PDFs for case X"
    - "generate the IMM forms for <person>'s application"
    - "I need IMM5645 + IMM5710 for <client>"
  NOT for: "fill the webform" / "open the portal" → use formbro-webform.
---

## Shared platform token (host agent — mandatory)

- Canonical durable user credential: `~/.jackyzhang.app/token/user.json` (`jz_` only).
- **One connect for the whole platform.** If this file already exists from any other official plugin, do **not** ask the human to log in again.
- On load, if `user.json` is missing but a legacy on-disk `jz_` exists (`token/user.json`, product-local config token fields), **migrate immediately** by running this product's CLI `whoami`/`doctor`/any authenticated command (load-time migrate is built in). User must feel no re-paste.
- Only when no migratable `jz_` exists: complete connect once using the token-delivery rules below. After that, every registered plugin reuses the same slot.
- Never print or log the raw token. Prefer masked doctor/whoami output.
- Do not create product-local durable token files.

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


# Fill IRCC IMM PDFs

Resolve the bundled CLI via `formbro-capabilities/SKILL.md` §B before running any command. The examples below use `<formbro>` as shorthand for that resolved binary.

This is the only agent-facing PDF skill. Use `formbro fill`; do not route PDF intent to the legacy `formbro export pdf` transport.

## Quick router

| User intent | Run |
|---|---|
| "fill IMM5257 + IMM5645 for case X" | `<formbro> fill --app-id <id> --forms IMM5257,IMM5645 -o ./out/` |
| "generate the PDF for this PR application" | `<formbro> fill --app-id <id> --forms IMM0008,IMM5406 -o ./out/` |
| "I'm not sure which forms are available" | `<formbro> programs schema <key> --role applicant`, then run `fill` with the selected forms |
| "fill the LMIA paperwork" | Reject PDF intent and direct the user to `formbro webform start`; LMIA is webform-only. |

## Behavior and success contract

- The CLI auto-detects TR vs PR unless `--program-key` is provided.
- The authenticated FormBro backend renders the requested PDFs through its saved-application `fill-pdf` endpoint.
- A single backend PDF is saved to the output directory. A multi-file ZIP is safely extracted there; archive paths cannot escape the output directory.
- Existing files are never overwritten. Name collisions use ` (1)`, ` (2)`, and so on.
- Success is synchronous JSON: `ok: true` plus a non-empty `files[]` array. Each file has `path`, `form`, and `size_bytes`.
- LMIA is rejected with a `formbro webform start` hint. Nothing is submitted automatically.

Example success:

```json
{
  "ok": true,
  "engine": "backend",
  "engine_detail": { "rendering": "server" },
  "category": "tr",
  "program_key": "sp-out",
  "app_id": "abc123",
  "files": [
    { "path": "./out/IMM1294.pdf", "form": "1294", "size_bytes": 683949 },
    { "path": "./out/IMM5645.pdf", "form": "5645", "size_bytes": 1592349 }
  ],
  "elapsed_ms": 312
}
```

Treat `ok: true` and the files at `files[].path` as the source of truth. PDF fills have no separate status command.

## Form coverage starting points

If you do not know which forms a program needs, run `formbro programs describe <key>` first.

| Program category | Common form set |
|---|---|
| TR study permit (`sp-out`, `sp-in`) | `IMM1294` / `IMM1295`, `IMM5645`, `IMM5709`, `IMM5710` |
| TR work permit (`wp-out`, `wp-in`) | `IMM1295` / `IMM5710`, `IMM5645`, `IMM5709` |
| TR visitor visa (`visa-out`, `visa-in`) | `IMM5257`, `IMM5645`, `IMM5709` |
| TR visitor record (`visitor-record`) | `IMM5708`, `IMM5645` |
| PR general / express entry | `IMM0008`, `IMM5406`, `IMM5562`, `IMM5669` |
| PR spouse / parent sponsorship | `IMM0008`, `IMM5406`, `IMM5532`, `IMM1344` |
| LMIA (`hws`, `lws`, `ee`) | No PDF flow; use `formbro webform start`. |

Unsupported forms and validation failures are returned as structured errors. Surface the backend's remediation fields and hints rather than retrying with another transport.

## Reference

```sh
<formbro> fill --app-id <id> --forms IMM<id>,IMM<id>,... [-o <dir>] [--program-key <key>]
```

- `--app-id` (required) — application id.
- `--forms` (required) — comma-separated four-digit form ids; the `IMM` prefix is optional.
- `-o, --output` (default `.`) — directory for the resulting PDFs; created if missing.
- `--program-key` (optional) — normally auto-detected. LMIA keys are rejected.

The old `--engine` switch is not part of the agent contract. Current compatibility builds accept `auto` or `backend` as server-rendered aliases and reject `local` with an actionable error.

## Parallelism

Use one `formbro fill` call for multiple forms in the same application. Calls for different applications are independent and may run in parallel.

## Legacy export boundary

`formbro export pdf` remains a legacy transport. `pdf-async`, `pdf-status`, and `pdf-result` have been removed; the backend no longer provides async PDF task endpoints. Use `formbro fill` for all agent PDF requests.

## Version

This skill requires a plugin version whose `formbro fill` output reports `engine: "backend"` with `engine_detail.rendering: "server"`. If the command is missing, refresh the installed plugin.
