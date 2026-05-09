---
name: formbro-read
description: Read-only operations. Use for any "find / search / list / get / status / audit" intent. Requires connect-formbro to have been run first. See formbro-capabilities for the full intent → command router.
---

# Read FormBro data

All commands shell out to the bundled `formbro` CLI (resolve via `runtime-manifest.json`). All output is JSON on stdout; structured errors go to stderr with non-zero exit.

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
