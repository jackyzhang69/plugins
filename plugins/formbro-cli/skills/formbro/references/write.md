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


# Write FormBro data

All commands shell out to the bundled `formbro` CLI. Resolve it once via `the formbro router/SKILL.md` §B and invoke that exact path; do not rely on ambient `PATH`.

**Cardinal rule:** every write that affects FormBro data must go through the bundled CLI. Never bypass this skill with direct backend requests or database writes. Every write that affects an existing entity should be preceded by a `validate` call (see end of this file) and, for status changes / deletes, an explicit user confirmation.

**`validate` ≠ webform fill-ready.** `validate` here checks the saved data model (schema + business rules) only — it does not exercise the government-portal adapter. If the case is headed to automated webform filling, `validate` passing is NOT sufficient on its own; `webform preflight` must also return clean (see `the formbro router` Rule 6 and the `references/webform.md` skill).

## External files: import vs extract vs patch

The CLI is intentionally JSON-first. It does **not** directly read Excel/CSV/DOCX/PDF/image file paths in the agent-native import flow. The agent reads local files, converts them to JSON or text, then sends that JSON/text through the CLI.

| Source material | New applicant + application | Existing entity update |
|---|---|---|
| Excel / XLSX / CSV tables | Parse locally to JSON, then `import contract` -> `import apply-json --dry-run` -> `import apply-json` | Parse locally to JSON, then `validate data` / `persons patch` / `applications patch` / `employers patch` |
| Word / DOCX | Extract text/tables locally, map to JSON using `import contract` | Extract text locally, then `extract contract` -> `extract apply-json` or direct patch |
| PDF / scanned PDF / image | OCR or extract text locally first; do not claim CLI reads the file | OCR/extract locally, then `extract text` or `extract apply-json` |
| Plain text / email body | Map text to contract JSON locally | `extract text --text "<text>"` can be used when the backend extractor is appropriate |
| Already structured JSON | `import apply-json --dry-run`, then save | `validate data`, then patch/apply-json |

New-case rule: use `import`. Existing-entity rule: use `extract`, `validate`, and `patch`. Do not clone existing database records to fake an import.

### New case from file(s): mandatory sequence

```sh
<formbro> import contract --program-key <key> [--target new-case]
# Agent reads user files locally and generates JSON matching the returned schema.
<formbro> import apply-json --program-key <key> --json '<json>' [--target new-case] --dry-run
<formbro> import apply-json --program-key <key> --json '<json>' [--target new-case]
```

If the imported case has related people that the import contract cannot save in one pass, create and attach them through CLI mutations only:

```sh
<formbro> persons create --program-key <key> --role spouse [...]
<formbro> persons patch <person_id> --data '<json_object>'
<formbro> applications replace-person --app-id <id> --role spouse --person-id <person_id>
<formbro> applications attach --app-id <id> --role dependent --person-id <person_id>
<formbro> validate person --person-id <person_id> --program-key <key> --role <role>
```

### Existing entity from file/text: mandatory sequence

```sh
<formbro> extract contract --program-key <key> --entity-type <T> [--sub-object-type <t>]
# Agent extracts local file/text into JSON matching the contract.
<formbro> validate data --entity-type <T> --data '<json_object>'
<formbro> extract apply-json --target-entity-id <id> --target-entity-type <applicant|application|employer> --json '<json>' [--program-key <key>] [--expected-version <v>]
```

When you already know the exact patch shape, direct patch commands are acceptable after validation:

```sh
<formbro> persons patch <person_id> --data '<json_object>' [--expected-version <v>]
<formbro> applications patch <app_id> --program-key <key> --data '<json_object>' [--expected-version <v>]
<formbro> employers patch <employer_id> --data '<json_object>'
```

### Safe field patches inside arrays

For a single field inside an existing array row, use `--set` with bracket indexes:

```sh
<formbro> persons patch <person_id> --program-key <key> --set 'education[0].country=China'
<formbro> applications patch <app_id> --program-key <key> --set 'backgrounds[0].background.education.details[0].country=China'
```

Do **not** send a whole array in `--data` just to update one field. Arrays are authoritative in normal JSON patches; a payload like `{"education":[{"country":"China"}]}` can replace the row and discard sibling fields. Indexed `--set` paths are converted by the CLI to backend path updates, preserving existing row fields.

## Quick router (user intent → exact command)

| If the user says… | Run |
|---|---|
| "validate this application before submit" | `<formbro> validate by-id --entity-type <T> --entity-id <id>` (entity_type = `<category-lc>-<program-key>-application`, e.g. `tr-sp-in-application`, `pr-general-application`, `lmia-hws-application`) |
| "validate this person for <program>" | `<formbro> validate person --person-id <id> --program-key <key> [--role applicant]` |
| "validate this raw data before I patch" | `<formbro> validate data --entity-type <T> --data '<json>'` |
| "start a new <program> application for <person>" | `<formbro> applications start --program-key <key> --applicant-id <id>` |
| "patch <field> on this application" | `<formbro> applications patch <id> --program-key <key> --set path=value` for scalar/indexed fields; use `--data '<json>'` only for intentional object/array replacement |
| "attach / replace / remove a person on application" | `<formbro> applications attach\|replace-person\|remove-person --app-id <id> --role <role> --person-id <id>` |
| "set application status to <new>" | `<formbro> applications set-status --app-id <id> --program-key <key> --status <new>` — **CONFIRM with user first** |
| "create a new person quickly" | `<formbro> persons create --program-key <key> --role <role> [--first-name …] [--last-name …]` |
| "create / update an LMIA employer" | `<formbro> employers create … / employers patch <id> --data '<json>'` |
| "import / create applicant + application from user file(s)" | `<formbro> import contract --program-key <key>` then local agent reads files directly, generates `{ "applicant": {...}, "application": {...} }`, runs `<formbro> import apply-json --program-key <key> --json '<json>' --dry-run`, then reruns without `--dry-run` if valid |
| "extract/patch data into an existing entity" | `<formbro> extract contract --program-key <key> --entity-type <T>` then local agent generates JSON and calls `<formbro> extract apply-json --target-entity-id <id> --target-entity-type <applicant|application|employer> --json '<json>' [--program-key <key>]` |
| "fill the IMM0008 / IMM5257 PDF for this case" | **Use `references/fill.md` skill** — call `<formbro> fill --app-id <id> --forms IMM0008,IMM5406 -o ./out/`. Do NOT call `export pdf` for agent purposes; `export pdf` is TR-route-only in cli and doesn't auto-detect category. |
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

### `forms` (for PDF generation — see `references/fill.md` for the agent path)
TR: `IMM5257`, `IMM5645`, `IMM5708`, `IMM5709`, `IMM5710`, `IMM1294`, `IMM1295` (subset varies by program)
PR: `IMM0008`, `IMM5406`, `IMM5532`, `IMM5562`, `IMM5669`, `IMM1344` (subset varies by program)
LMIA: ❌ not applicable — LMIA is webform-only.

If the user names a form the program doesn't support, the CLI returns a 400 with the supported set — surface that error.

> **Agent rule of thumb for PDFs:** call `formbro fill` (in the `references/fill.md` skill). Only fall back to the `export pdf*` family below for explicit advanced flows the user asks for (raw data preview, sync batch, blank template).

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
<formbro> applications patch <app_id> --program-key <key> --data '<json_object>' [--set 'path=value'] [--expected-version <v>]

# Persons / employers
<formbro> persons create --program-key <key> --role <role> [...]
<formbro> persons patch <person_id> --program-key <key> --data '<json_object>' [--set 'path=value'] [--expected-version <v>]
<formbro> employers create --company-name <name> [...]
<formbro> employers patch <employer_id> --data '<json_object>'
<formbro> employers delete <employer_id>     # CONFIRM first

# Notes / uploads
<formbro> notes add --entity-id <id> --entity-type <T> [--program-key <key>] --note "<text>"
<formbro> uploads slots --entity-type <T> --entity-id <id>

# Extract & apply
<formbro> import contract --program-key <key> [--target new-case]
<formbro> import apply-json --program-key <key> --json '<{"applicant":{...},"application":{...}}>' [--target new-case] [--dry-run]
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
<formbro> export entity   --entity-type <T> --entity-id <id> --output <path> [--language en|fr] [--program-key <key>] [--blank]
<formbro> export data     --form-id <FORM> --data '<json>' --output <path> [--program-key <key>] [--language en|fr]
<formbro> export template --form-id <FORM> --output <path> [--language en|fr]
<formbro> export pdf      --program-key <key> --app-id <id> --forms <FORM,FORM,...> [--output <path>]
<formbro> export pdf-check  --program-key <key> --app-id <id> --forms <FORM,FORM,...>
<formbro> export extension  --program-key <key> --app-id <id>
```
