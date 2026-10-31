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

The dispatch-only `source-watch` workflow is retired and only directs operators
to AnyCase's canonical workflow. If invoked, its notice job still uses the
governed `[self-hosted, macOS, ARM64]` runner capability labels; it does not
restart source watching or deploy anything.

## Change a grid without rebuilding

Policy YAML is data. The server binary only interprets the current rows in Supabase Postgres (`anyscore.policy_versions`).

1. Edit `policies/<area>/<grid>.yaml` and bump `revision` (a revision is immutable once synced).
2. Sync into Supabase. Set `ANYSCORE_SUPABASE_DB_URL` in the environment (do not pass the URI as an argument). This does not compile the server:

```bash
anyscore-policy-sync \
  --policies policies \
  --schema schema/policy-schema.json
```

3. Reload the running server: send `SIGHUP`, or `POST /api/v1/anyscore/policies/reload`.
4. `evaluate` uses the new points. The same policy bytes still score the same way; only the stored payload changed.

`schema/policy-schema.json` is the write constraint (`$id` is stored as `schema_version`). The image build ignores `policies/`, so a points change does not rebuild the image. DDL lives at `crates/anyscore-core/migrations/001_policy_versions.sql` and is applied by the sync tool.
