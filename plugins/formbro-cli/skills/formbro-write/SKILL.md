---
name: formbro-write
description: Mutations on applicants, applications, employers, persons, notes, uploads. Always validate before patching. Confirm with user before delete or status change. Requires connect-formbro to have been run first.
---

# Write FormBro data

All commands shell out to the bundled `formbro` CLI (resolve via `runtime-manifest.json`).

**Cardinal rule:** every write that affects an existing entity should be preceded by a `validate` call (see end of this file) and, for status changes / deletes, an explicit user confirmation.

## Applications lifecycle

```sh
# Start a new application
<formbro> applications start --program-key <key> [--applicant-id <id>]

# Attach / replace / remove a person on an application
<formbro> applications attach        --app-id <id> --role <role> --person-id <id>
<formbro> applications replace-person --app-id <id> --role <role> --person-id <id>
<formbro> applications remove-person  --app-id <id> --role <role> --person-id <id>

# Change status — CONFIRM with user first
<formbro> applications set-status --app-id <id> --program-key <key> \
                                  --status <new_status> [--expected-version <v>]

# Patch arbitrary application case data
<formbro> applications patch <app_id> --program-key <key> --data '<json_object>' \
                                      [--expected-version <v>]
```

## Persons (applicant / spouse / child / etc.)

```sh
# Create a person quickly (program-aware)
<formbro> persons create --program-key <key> --role <role> \
                         [--first-name <n>] [--last-name <n>] [--email <e>]

# Patch person fields
<formbro> persons patch <person_id> --data '<json_object>' [--expected-version <v>]
```

## Employers

```sh
<formbro> employers create --company-name <name> [--business-number <bn>] \
                           [--contact-first-name ...] [--contact-last-name ...]

<formbro> employers patch <employer_id> --data '<json_object>'

<formbro> employers delete <employer_id>     # CONFIRM with user first
```

## Notes

```sh
<formbro> notes add --entity-id <id> --entity-type <applicant|application|employer> \
                    [--program-key <key>] --note "<text>"
```

## Uploads (file attachment slots)

```sh
<formbro> uploads slots --entity-type <type> --entity-id <id>
```

The slots response gives per-file pre-signed PUT URLs. The actual file PUT is left to the user / a follow-up tool.

## Extract & apply (LLM-assisted JSON extraction)

```sh
# Inspect contract (what fields the extractor targets)
<formbro> extract contract --program-key <key> --entity-type <type> [--sub-object-type <t>]

# Run extraction from raw text
<formbro> extract text --text "<text>" --entity-type <type> [--program-key <key>] \
                       [--entity-id <id>] [--model <model>]

# Apply previously-extracted JSON onto an entity (idempotency-keyed)
<formbro> extract apply-json --target-entity-id <id> --target-entity-type <applicant|application|employer> \
                             --json '<json_object>' [--program-key <key>] [--expected-version <v>]

# Inspect models / formats / tasks
<formbro> extract models
<formbro> extract formats
<formbro> extract task-status <task_id>
```

## Always-validate-before-apply

```sh
<formbro> validate data       --entity-type <type> --data '<json_object>' [--tab <tab>]
<formbro> validate by-id      --entity-type <type> --entity-id <id> [--tab <tab>]
<formbro> validate person     --person-id <id> --program-key <key> [--role applicant]
<formbro> validate operation  --operation <op> --entity-type <type> [--entity-id <id> | --entity-data '<json>']
```

## Optimistic concurrency

Patches accept `--expected-version <n>`. Use this whenever you have just read the entity — if someone else patched it in the meantime, the call fails with a clean conflict error and you re-read + re-merge.
