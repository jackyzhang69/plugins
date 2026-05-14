---
name: formbro-read
description: "Read-only FormBro operations — find, list, get, status, audit. For person-to-application-id lookup use the canonical `find --include applications` path or `applications resolve`. Requires connect-formbro first. Full router: see formbro-capabilities."
when_to_use: "Trigger phrases: find/search/look up a person; what's the status of a case; list applications; what applications does someone have; show employer / audit log / programs."
---

# Read FormBro data

All commands shell out to the bundled `formbro` CLI (resolve via `formbro-capabilities/SKILL.md` §B). All output is JSON on stdout; structured errors go to stderr with non-zero exit.

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

**Important matching rule** (programs with older data): backend may return `program_key: ""` (empty) for some applications. Both `resolve` and the v1.5.0 `webform start-by-name` already match by `app.program_key` OR the normalised `program` field (`"PR General"` → strip `"PR "` → lowercase → `"general"`). Don't hand-roll this match yourself; use the CLI.

**`list` vs `inventory` semantic distinction** (read carefully):
- `applications list` calls `/api/dashboard/applications` — filters by active status (draft / in_progress / submitted / in_review / additional_documents_requested). Use this when you want the consultant's active workbench.
- `applications inventory` calls `/api/mcp/find` wildcard — returns EVERY application regardless of status (incl. empty/archived). Use this for batch tests, audit sweeps, finding cases with bad seed data, or any "give me all" intent.

If `list` returns empty unexpectedly, try `inventory` to confirm whether the data exists at all vs whether it's just non-active.

## Quick router (user intent → exact command)

| If the user says… | Run |
|---|---|
| "find Meili Wang" / "search for ABC Corp" | `<formbro> find "<query>"` — start here for any name-based lookup |
| "what's the status of application X" | `<formbro> applications get X` (works for TR / PR / LMIA) |
| "list this week's PR cases" | `<formbro> applications list --program <pr-key> --limit 50` |
| "which applications are stuck in DRAFT > 14 days" | `<formbro> applications by-status DRAFT --days-in-status 14` |
| "show employer ABC Corp's LMIAs" | `<formbro> employers list --search "ABC"` then `employers get <id>` |
| "what programs do you support" | `<formbro> programs list` |
| "what fields does a PR general application need for the applicant role" | `<formbro> programs schema general --role applicant` |
| "what changes did I make today" | `<formbro> audit my --from <today>` |

## All commands (reference)

```sh
# Cross-entity search — start here for name lookups
<formbro> find "<query>" [--program <key>] [--include applications,documents] [--limit 10]

# Programs (registry; cross-program metadata)
<formbro> programs list
<formbro> programs describe <program_key>
<formbro> programs schema <program_key> --role <role>

# Applicants (person records; shared across TR/PR/LMIA)
<formbro> applicants list [--search <text>] [--program <key>] [--limit 20]
<formbro> applicants get <applicant_id>

# Applications (TR/PR/LMIA cases)
<formbro> applications list [--status <status>] [--program <key>] [--limit 20] [--offset 0]
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
- For TR / PR / LMIA program-key values, see `formbro-capabilities/SKILL.md` §2 — **never invent keys** like `pr-general-application`; the actual key is `general` (program registry handles the prefix).
- Don't enumerate every possible interpretation of an ambiguous query — pick the most likely one, run it, surface the result, ask if user wanted something else.

## What this skill is NOT for

- No mutations. For create/update/delete, see `formbro-write`.
- No webform automation. For local Playwright fills, see `formbro-webform`.
