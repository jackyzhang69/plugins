# Score playbook

- **CLI**: `anyscore policies|inputs|evaluate|rank|whatif|diff`. Translates missing inputs into a typed envelope. Never scores locally. Never saves client files.
- **Host agent**: read `anyscore inputs <id>`; derive factors from the consultant's existing materials using each field's official definition; ask human-only facts once; write inputs JSON + a local `<name>.notes.json` sidecar; call evaluate; explain in plain language.
- **Optional map**: when facts are declared but need confirmatory mapping onto official enums, `POST /api/v1/anyscore/map` (product JWT). Callers must already hold scoring facts (age years, CLB levels, etc.). Map does not convert IELTS→CLB.

## Fill then evaluate

1. `anyscore inputs <policy_id> --json`
2. Derive every `required_for_score` factor from evidence (or confirmatory map when appropriate).
3. Write inputs JSON. Notes go in a sidecar, never in the inputs file.
4. `anyscore evaluate --policy <id> --inputs <file.json> --json`

## How to speak

- Lead with scores and whether each grid's listed minimums are met, not met, or incomplete.
- Never say the person is eligible or qualified.

## Commands

```bash
anyscore policies [--changed-since <revision-or-date>]
anyscore inputs <policy_id>
anyscore evaluate --policy <id> --inputs <file.json> [--as-of YYYY-MM-DD] [--resume <token>]
anyscore rank --inputs <file.json> [--as-of YYYY-MM-DD]
anyscore whatif --policy <id> --inputs <file.json> --changes <changes.json> [--as-of YYYY-MM-DD]
anyscore diff <old-result.json> <new-result.json>
```

## Session change check

1. Read `~/.jackyzhang.app/anyscore/score-check.json` for `max_revision` if it exists. That file is not client data.
2. Call `anyscore policies --changed-since <max_revision>` (or without the flag on first use).
3. The CLI updates `score-check.json` after a successful list.

If the consultant says 再考、如果语言、加一年经验、换工作、分数会不会、差多少分, write a `[{field, value}]` changes file and call `anyscore whatif`. Do not invent advice text. Report before / after / delta only.
