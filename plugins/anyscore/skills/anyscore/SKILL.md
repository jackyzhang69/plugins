---
name: anyscore
description: >-
  READ THIS FIRST for AnyScore. Canadian immigration points-grid engine:
  Express Entry and provincial grids. Connect / login, evaluate, rank,
  what-if, tell Jacky. One discovery file. Ask `anyscore commands --json`,
  never from memory of an older release.
when_to_use: |-
  Load on plugin start. points grid, CRS, Express Entry score, PNP score,
  what-if language, if I retake IELTS, how many points, 算分, 分数会不会,
  差多少分. Case law and CLB conversion are not this product.
---

# AnyScore — Canadian Immigration Points Grids

## Live CLI surface (fail-closed)

```bash
"$ANYSCORE_BIN" commands --json
```

Playbooks: [connect](references/connect.md), [score](references/score.md),
[tell-jacky](references/tell-jacky.md).

Public commands: `login`, `logout`, `whoami`, `doctor`, `policies`, `inputs`,
`evaluate`, `rank`, `whatif`, `diff`, `feedback`, `commands`.

## Talk to the human (Mandatory Interaction Protocol)

- **Plain language first.** Speak so a consultant can follow. Do not dump CLI flags, JSON field names, or HTTP codes unless they asked for detail.
- **Report outcomes, not machine steps.** Lead with the grid score and whether listed minimums are met, not met, or incomplete.
- **Never say eligible or qualified.** This is a policy-grid calculation only. Program eligibility is the consultant's judgment.
- **Mask secrets.** Tokens never appear in chat.

## Available Capabilities

1. **List grids (`policies`)**: live policy list. Use `--changed-since` after the first check.
2. **Input contract (`inputs`)**: official factor definitions. The host agent derives factors from evidence; the engine does not infer CLB, age, experience, or education.
3. **Evaluate (`evaluate`)**: one grid from an inputs JSON file.
4. **Rank (`rank`)**: every full-coverage grid from one inputs file.
5. **What-if (`whatif`)**: isolated field changes. Report before / after / delta only.
6. **Tell Jacky (`feedback`)**: playbook [tell-jacky](references/tell-jacky.md).

## Tool templates

```bash
anyscore policies
anyscore inputs <policy_id>
anyscore evaluate --policy <id> --inputs <file.json> [--as-of YYYY-MM-DD]
anyscore rank --inputs <file.json> [--as-of YYYY-MM-DD]
anyscore whatif --policy <id> --inputs <file.json> --changes <changes.json>
anyscore diff <old-result.json> <new-result.json>
anyscore login --token-stdin
anyscore logout
anyscore whoami
anyscore doctor
anyscore feedback --type <feature-request|bug-report|knowledge-tip> --title "<short>" --description "<details>" --user-confirmed
```

Do not add flags that are not in these templates. Do not invent an `eligible` field.
