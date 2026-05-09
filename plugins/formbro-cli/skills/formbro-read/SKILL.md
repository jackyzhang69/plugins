---
name: formbro-read
description: Read-only operations across applicants, applications, employers, programs, audit log. Use for any "find / get / list / status" request about FormBro data. Requires connect-formbro to have been run first.
---

# Read FormBro data

All commands shell out to the bundled `formbro` CLI (resolve via `runtime-manifest.json`). All output is JSON on stdout; structured errors go to stderr with non-zero exit.

## Smart search across applicants/applications/employers (start here)

```sh
<formbro> find "<query>" [--program <key>] [--include applications,documents] [--limit 10]
```

Use this first when the user says "find Meili Wang" or "show cases for ABC Corp" — it's the single best entry point.

## Programs

```sh
<formbro> programs list
<formbro> programs describe <program_key>
<formbro> programs schema <program_key> --role applicant
```

## Applicants

```sh
<formbro> applicants list [--search <text>] [--program <key>] [--limit 20]
<formbro> applicants get <applicant_id>
```

## Applications

```sh
<formbro> applications list [--status <status>] [--program <key>] [--limit 20] [--offset 0]
<formbro> applications by-status <status> [--program-key <key>] [--days-in-status <n>] [--limit 20]
<formbro> applications get <application_id>
<formbro> applications status <application_id>
<formbro> applications persons <application_id>
<formbro> applications documents <application_id>
```

## Employers

```sh
<formbro> employers list [--search <text>] [--stream hws] [--limit 20]
<formbro> employers get <employer_id>
```

## Audit (the user's own audit log)

```sh
<formbro> audit my [--from <YYYY-MM-DD>] [--to <YYYY-MM-DD>] [--action <pattern>] [--limit 50]
```

## Output handling

- All output is JSON. Parse with `jq` or equivalent when chaining.
- Errors print structured JSON to stderr and exit non-zero — surface the message clearly to the user.
- Sensitive fields (passport_number, SIN, encrypted_credentials) are auto-masked by the CLI unless `--unmask` is explicitly passed (admin only). Do not unmask in normal flows.

## What this skill does NOT do

- No mutations. For create/update/delete, use `formbro-write`.
- No webform automation. For local Playwright fills, use `formbro-webform`.
