# formbro-cli — Codex App plugin

Canadian immigration form automation. Wraps the [FormBro](https://formbro.ca) Rust CLI in Codex/Claude skills.

## What it does

| Skill | Purpose |
|---|---|
| [`formbro`](./skills/formbro/SKILL.md) | The only discovery file. Connect, read, write, fill, webform, and tell-jacky playbooks live under `skills/formbro/references/`. |

## Agent contract

- All FormBro reads, writes, validation, fill/export, uploads, notes, audit, and webform operations must go through the bundled `formbro` CLI.
- Agents may read local Excel/CSV/Word/DOCX/PDF/image/text files themselves, but saving or validating extracted data must go through the CLI.
- New cases from files use `formbro import contract` -> local JSON generation -> `formbro import apply-json --dry-run` -> `formbro import apply-json`.
- Existing entities from files/text use `formbro extract contract` / local JSON generation / `validate` / `extract apply-json` or the relevant `patch` command.
- The CLI does not directly accept local file paths for agent-native import. There is no `--file` import command; file parsing is the agent's job.
- Runtime truth is the bundled binary's `commands --json` plus the `formbro` router. Do not use stale examples such as `webform start-by-name` or legacy `--program` flags.

## The only thing the user inputs

A FormBro API token (starts with `jz_…`).

The user generates it once at https://jackyzhang.app/account/tokens. The host agent then pipes a token file: `printf %s "$(cat -- "$TOKEN_FILE")" | formbro login --token-stdin`. Do not tell the human to type it in a terminal. The CLI writes `~/.formbro/config.json` (or `%USERPROFILE%\.formbro\config.json` on Windows). Every other skill reads from that config — the token never enters argv, shell history, or logs; it is never asked for again, never echoed, and never stored anywhere except that single file.

## Token safety (enforced by skill text)

- Token is **never** printed. Skills mask any `jz_*` value as `jz_***`.
- Token is **never** written into any file other than the CLI's own `config.json`.
- Token is **never** transmitted to anything other than `https://formbro-api.jackyzhang.app` (the value baked into `runtime-manifest.json`).

## Per-platform binary selection

`runtime-manifest.json` declares:

| Platform key | Binary path |
|---|---|
| `darwin-arm64` | `bin/darwin-arm64/formbro` |
| `win32-x64` | `bin/win32-x64/formbro.exe` |

Current marketplace builds ship bundled binaries for macOS Apple Silicon and Windows x64 only. Other platforms must use a separately installed `FORMBRO_BIN`/PATH binary until a signed bundle is published for that platform.

Each skill resolves `${process.platform}-${process.arch}` against `binary.platforms`, verifies the matching `.sha256` sidecar, marks the file executable on POSIX, then shells out to it.

## Install

See the repo-root [README](../../README.md) for `codex plugin marketplace add` / `codex plugin install` commands.

## Versioning

The plugin's `version` (in `.codex-plugin/plugin.json`) is **independent** of the FormBro product semver. Plugin bumps follow plugin-schema needs, not FormBro release cadence.

## What this plugin is NOT

- Not an MCP server. The former public MCP endpoint is decommissioned; use the bundled CLI for agent integrations.
- Not a Claude Desktop / Claude Code adapter — Codex App only.
- Not a re-implementation of FormBro logic — every skill shells out to `formbro <subcommand>`.
- Not a place to store credentials in source.

## First-run note

`formbro fill` needs only the bundled CLI plus backend connectivity. It does not
download or execute a local PDF runtime. `formbro doctor --fetch` is reserved for
pre-fetching the local webform worker used by `formbro webform ...`.
