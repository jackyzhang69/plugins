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


# Read FormBro data

All commands shell out to the bundled `formbro` CLI. Resolve it once via `the formbro router/SKILL.md` §B and invoke that exact path; do not rely on ambient `PATH`. All output is JSON on stdout; structured errors go to stderr with non-zero exit.

## Person → application_id (THE canonical path)

For any "fill for `<person>`" / "find `<person>`'s case" intent, this is the standard chain:

**Option 1 — `find` with applications included** (most flexible; gives full context):
```bash
formbro find "<person name>" --include applications --limit 10
```
Sample response:
```json
{
  "results": [
    {
      "id": "6a062b…", "name": "Meili Wang",
      "email": "…", "phone": "…",
      "applications": [
        { "id": "<application_id>", "program": "PR General",
          "program_key": "general", "status": "in_progress",
          "created": "2026-05-14" }
      ]
    }
  ],
  "total": 1
}
```

**Option 2 — `applications resolve`** (one-shot, returns unique/ambiguous/none):
```bash
formbro applications resolve --query "<person>" --program-key <key>
```
Returns `{match: "unique", application_id: "…", applicant_name: "…"}` directly, or `{match: "ambiguous", candidates: [...]}` / `{match: "none"}`.

**Important matching rule** (programs with older data): backend may return `program_key: ""` (empty) for some applications. Both `resolve` and v1.5.1's `webform start --query` already match by `app.program_key` OR the normalised `program` field (`"PR General"` → strip `"PR "` → lowercase → `"general"`). Don't hand-roll this match yourself; use the CLI.

**`list` vs `inventory` semantic distinction** (read carefully):
- `applications list` calls `/api/dashboard/applications` — filters by active status (draft / in_progress / submitted / in_review / additional_documents_requested). Use this when you want the consultant's active workbench.
- `applications inventory` calls `/api/mcp/find` wildcard — returns EVERY application regardless of status (incl. empty/archived). Use this for batch tests, audit sweeps, finding cases with bad seed data, or any "give me all" intent.

If `list` returns empty unexpectedly, try `inventory` to confirm whether the data exists at all vs whether it's just non-active.

## Quick router (user intent → exact command)

| If the user says… | Run |
|---|---|
| "find Meili Wang" / "search for ABC Corp" | `<formbro> find "<query>"` — start here for any name-based lookup |
| "what's the status of application X" | `<formbro> applications get X` (works for TR / PR / LMIA) |
| "list this week's PR cases" | `<formbro> applications list --program-key <pr-key> --limit 50` |
| "which applications are stuck in DRAFT > 14 days" | `<formbro> applications by-status DRAFT --days-in-status 14` |
| "show employer ABC Corp's LMIAs" | `<formbro> employers list --search "ABC"` then `employers get <id>` |
| "what programs do you support" | `<formbro> programs list` |
| "what fields does a PR general application need for the applicant role" | `<formbro> programs schema general --role applicant` |
| "what changes did I make today" | `<formbro> audit my --from <today>` |

## All commands (reference)

```sh
# Cross-entity search — start here for name lookups
<formbro> find "<query>" [--program-key <key>] [--include applications,documents] [--limit 10]

# Programs (registry; cross-program metadata)
<formbro> programs list
<formbro> programs describe <program_key>
<formbro> programs schema <program_key> --role <role>

# Applicants (person records; shared across TR/PR/LMIA)
<formbro> applicants list [--search <text>] [--program-key <key>] [--limit 20]
<formbro> applicants get <applicant_id>

# Applications (TR/PR/LMIA cases)
<formbro> applications list [--status <status>] [--program-key <key>] [--limit 20] [--offset 0]
<formbro> applications by-status <status> [--program-key <key>] [--days-in-status <n>] [--limit 20]
<formbro> applications get <application_id>
<formbro> applications status <application_id>           # backend-persisted status
<formbro> applications persons <application_id>
<formbro> applications documents <application_id>

# Employers (LMIA only — TR/PR have no employer entity)
<formbro> employers list [--search <text>] [--stream hws|lws|ee] [--limit 20]
<formbro> employers get <employer_id>

# The user's own audit log
<formbro> audit my [--from <YYYY-MM-DD>] [--to <YYYY-MM-DD>] [--action <pattern>] [--limit 50]
```

## Execution + output rules

- All output is JSON. Pipe through `jq` when chaining.
- Sensitive fields (`passport_number`, `SIN`, `encrypted_credentials`) are auto-masked by the CLI. Do not pass `--unmask` (admin-only; surfaces real values).
- For TR / PR / LMIA program-key values, see `the formbro router/SKILL.md` §2 — **never invent keys** like `pr-general-application`; the actual key is `general` (program registry handles the prefix).
- Don't enumerate every possible interpretation of an ambiguous query — pick the most likely one, run it, surface the result, ask if user wanted something else.

## What this skill is NOT for

- No mutations. For create/update/delete, see `references/write.md`.
- No webform automation. For local Playwright fills, see `references/webform.md`.
