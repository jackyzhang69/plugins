# AnyScore

Canadian immigration points-grid engine. Official Jacky plugin.

The engine converts declared policy-grid factors into a source-cited `grid_assessment`. It does not infer CLB, age, experience, or education. It never outputs `eligible` or `qualified`. Program eligibility stays with the consultant.

## What it does

- Lists live Express Entry and provincial points grids
- Returns the official input contract for each grid
- Evaluates one grid, ranks every full-coverage grid, and runs isolated what-if changes
- Speaks in scores and listed minimums: met, not met, or incomplete

## Commands

```bash
anyscore login --token-stdin
anyscore policies
anyscore inputs <policy_id>
anyscore evaluate --policy <id> --inputs <file.json>
anyscore rank --inputs <file.json>
anyscore whatif --policy <id> --inputs <file.json> --changes <changes.json>
anyscore doctor
anyscore feedback --type feature-request --title "…" --description "…" --user-confirmed
```

Ask the live surface with `anyscore commands --json`. Do not invent flags.

## Auth

One Portal user token for the whole platform: `~/.jackyzhang.app/token/user.json`. AnyScore exchanges that token for `aud=anyscore`. Runtime files stay under `~/.jackyzhang.app/anyscore/`.

## Public API

`https://anyscore.jackyzhang.app/api/v1/anyscore/*`

This repository also builds the deterministic server. The official client is the `anyscore` CLI on macOS Apple Silicon and Windows x64. Linux is the server host, not a public client platform.

## Verify

```bash
cargo test --workspace
bash scripts/verify-package
```
