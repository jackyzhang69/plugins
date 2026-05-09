---
name: formbro-write
description: Mutations + extraction + validation + PDF/Excel export. Validate before patching. Confirm with user before delete or status-change. Requires connect-formbro to have been run first. See formbro-capabilities for the full intent → command router and parameter cheat-sheets.
---

# Write FormBro data

All commands shell out to the bundled `formbro` CLI (resolve via `runtime-manifest.json`).

**Cardinal rule:** every write that affects an existing entity should be preceded by a `validate` call (see end of this file) and, for status changes / deletes, an explicit user confirmation.

## Quick router (user intent → exact command)

| If the user says… | Run |
|---|---|
| "validate this application before submit" | `<formbro> validate by-id --entity-type <T> --entity-id <id>` (entity_type = `<category-lc>-<program-key>-application`, e.g. `tr-sp-in-application`, `pr-general-application`, `lmia-hws-application`) |
| "validate this person for <program>" | `<formbro> validate person --person-id <id> --program-key <key> [--role applicant]` |
| "validate this raw data before I patch" | `<formbro> validate data --entity-type <T> --data '<json>'` |
| "start a new <program> application for <person>" | `<formbro> applications start --program-key <key> --applicant-id <id>` |
| "patch <field> on this application" | `<formbro> applications patch <id> --program-key <key> --data '<json>'` |
| "attach / replace / remove a person on application" | `<formbro> applications attach\|replace-person\|remove-person --app-id <id> --role <role> --person-id <id>` |
| "set application status to <new>" | `<formbro> applications set-status --app-id <id> --program-key <key> --status <new>` — **CONFIRM with user first** |
| "create a new person quickly" | `<formbro> persons create --program-key <key> --role <role> [--first-name …] [--last-name …]` |
| "create / update an LMIA employer" | `<formbro> employers create … / employers patch <id> --data '<json>'` |
| "extract data from this text/document" | `<formbro> extract text --text "<text>" --entity-type <T> [--program-key <key>]` then `<formbro> extract apply-json --target-entity-id <id> --target-entity-type <T> --json '<json>'` |
| "fill the IMM0008 / IMM5257 PDF for this case" | **Use `formbro-fill` skill** — call `<formbro> fill --app-id <id> --forms IMM0008,IMM5406 -o ./out/`. Do NOT call `export pdf` for agent purposes; `export pdf` is TR-route-only in cli and doesn't auto-detect category. |
| "export this applicant / application as Excel" | `<formbro> export entity --entity-type <T> --entity-id <id> --output app.xlsx` |
| "what files can I attach to this case" | `<formbro> uploads slots --entity-type <T> --entity-id <id>` |
| "add a note to <case>" | `<formbro> notes add --entity-id <id> --entity-type <T> --note "<text>"` |

## Parameter cheat-sheet (do not guess)

### `program-key` values
TR: `sp-out`, `sp-in`, `wp-out`, `wp-in`, `visa-out`, `visa-in`, `visitor-record`
PR: `general`, `express-entry`, `caregiver`, `spouse-sponsorship`, `parent-sponsorship`, `renewal`
LMIA: `hws`, `lws`, `ee`

### `entity-type` values (used by `validate by-id`, `export entity`, `extract apply-json`)
Format is `<category-lc>-<program-key>-<role>`:

- `tr-sp-in-applicant`, `tr-sp-in-application`, `tr-sp-in-spouse`, `tr-sp-in-dependant`
- `tr-wp-out-applicant`, `tr-visa-in-applicant`, `tr-visitor-record-applicant`
- `pr-general-applicant`, `pr-general-application`, `pr-general-spouse`, `pr-general-dependant`
- `pr-express-entry-applicant`, `pr-spouse-sponsorship-sponsor`, `pr-caregiver-applicant`
- `lmia-hws-employer`, `lmia-hws-application`, `lmia-lws-employer`, `lmia-ee-employer`

If the exact entity-type is uncertain, run `<formbro> programs schema <program-key> --role <role>` to discover it from the registry. Do not invent slugs.

### `forms` (for PDF generation — see `formbro-fill` for the agent path)
TR: `IMM5257`, `IMM5645`, `IMM5707`, `IMM5708`, `IMM5709`, `IMM5710`, `IMM1294`, `IMM1295` (subset varies by program)
PR: `IMM0008`, `IMM5406`, `IMM5532`, `IMM5562`, `IMM5669`, `IMM1344` (subset varies by program)
LMIA: ❌ not applicable — LMIA is webform-only.

If the user names a form the program doesn't support, the CLI returns a 400 with the supported set — surface that error.

> **Agent rule of thumb for PDFs:** call `formbro fill` (in the `formbro-fill` skill). Only fall back to the `export pdf*` family below for explicit advanced flows the user asks for (raw data preview, async batch, blank template).

## Optimistic concurrency

Patches accept `--expected-version <n>`. Use this whenever you have just read the entity — if someone else patched it in the meantime, the call fails with a clean conflict error and you re-read + re-merge.

## Reference (full subcommand list)

```sh
# Applications lifecycle
<formbro> applications start --program-key <key> [--applicant-id <id>]
<formbro> applications attach        --app-id <id> --role <role> --person-id <id>
<formbro> applications replace-person --app-id <id> --role <role> --person-id <id>
<formbro> applications remove-person  --app-id <id> --role <role> --person-id <id>
<formbro> applications set-status --app-id <id> --program-key <key> --status <new> [--expected-version <v>]
<formbro> applications patch <app_id> --program-key <key> --data '<json_object>' [--expected-version <v>]

# Persons / employers
<formbro> persons create --program-key <key> --role <role> [...]
<formbro> persons patch <person_id> --data '<json_object>' [--expected-version <v>]
<formbro> employers create --company-name <name> [...]
<formbro> employers patch <employer_id> --data '<json_object>'
<formbro> employers delete <employer_id>     # CONFIRM first

# Notes / uploads
<formbro> notes add --entity-id <id> --entity-type <T> [--program-key <key>] --note "<text>"
<formbro> uploads slots --entity-type <T> --entity-id <id>

# Extract & apply
<formbro> extract contract --program-key <key> --entity-type <T> [--sub-object-type <t>]
<formbro> extract text --text "<text>" --entity-type <T> [--program-key <key>] [--entity-id <id>] [--model <model>]
<formbro> extract apply-json --target-entity-id <id> --target-entity-type <applicant|application|employer> --json '<json>' [--program-key <key>] [--expected-version <v>]
<formbro> extract models | extract formats | extract task-status <id>

# Validate
<formbro> validate data       --entity-type <T> --data '<json_object>' [--tab <tab>]
<formbro> validate by-id      --entity-type <T> --entity-id <id> [--tab <tab>]
<formbro> validate person     --person-id <id> --program-key <key> [--role applicant]
<formbro> validate operation  --operation <op> --entity-type <T> [--entity-id <id> | --entity-data '<json>']

# Export
<formbro> export entity   --entity-type <T> --entity-id <id> --output <path> [--language en|fr] [--program <key>] [--blank]
<formbro> export data     --form-id <FORM> --data '<json>' --output <path> [--program <key>] [--language en|fr]
<formbro> export template --form-id <FORM> --output <path> [--language en|fr]
<formbro> export pdf      --program <key> --app-id <id> --forms <FORM,FORM,...> [--output <path>]
<formbro> export pdf-async --program <key> --app-id <id> --forms <FORM,FORM,...>
<formbro> export pdf-status <task_id>
<formbro> export pdf-result <task_id> --output <path>
<formbro> export pdf-check  --program <key> --app-id <id> --forms <FORM,FORM,...>
<formbro> export extension  --program <key> --app-id <id>
```
