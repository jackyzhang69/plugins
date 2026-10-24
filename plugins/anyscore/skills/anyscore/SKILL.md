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

## Token delivery to the host agent (connect) — LOCKED 2026-08-14

The host agent performs connect **for** the human. **Never** tell the human to open a terminal and run login commands themselves.

Accept input in this order:

1. **File containing the token (preferred).** If the human provides a filesystem path (e.g. `~/Desktop/jacky-token.txt`) or an attached/readable file whose contents are a single `jz_…` value (optional surrounding whitespace/newline only):
   - Read the file in the agent tool channel.
   - Pipe the token to the product CLI via stdin only: `"$ANYSCORE_BIN" login --token-stdin`
   - Do **not** put the token on argv, in chat echo, in logs, or in screenshots.
   - Confirm success with masked doctor/whoami only.

2. **Plaintext token in chat (allowed, discouraged).** If the human pastes a raw `jz_…` into the conversation:
   - **Warn once** in plain language: prefer a local file path next time so the secret is not retained in chat history; do **not** refuse.
   - Proceed immediately: feed that value to `login --token-stdin` via your non-echoing bash/exec tool (`printf '%s' "$TOKEN" | "$ANYSCORE_BIN" login --token-stdin`).
   - Do not repeat the raw token back in the reply.

3. **No token yet.** Ask the human to send either a **file path** (best) or paste the token. Still do not ask them to run terminal commands.

Hard rules:

- `--token <value>` / argv secrets remain **forbidden** for the CLI.
- After any successful connect, other official plugins must not re-prompt when `user.json` is present.
- Confirm success with masked output: `"$ANYSCORE_BIN" whoami --json`.
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
