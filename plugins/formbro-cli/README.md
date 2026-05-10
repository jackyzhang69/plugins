# formbro-cli — Codex App plugin

Canadian immigration form automation. Wraps the [FormBro](https://formbro.ca) Rust CLI in 4 Codex skills.

## What it does

| Skill | Purpose |
|---|---|
| [`connect-formbro`](./skills/connect-formbro/SKILL.md) | One-time setup. Capture FormBro API token → persist via CLI. **Run first.** |
| [`formbro-capabilities`](./skills/formbro-capabilities/SKILL.md) | Agent consumption contract: intent → command router, PR/TR/LMIA support matrix, parameter cheat-sheets, status truth model. **Read second.** |
| [`formbro-read`](./skills/formbro-read/SKILL.md) | Search / list / inspect applicants, applications, employers, programs, audit log. |
| [`formbro-write`](./skills/formbro-write/SKILL.md) | Create / patch / delete persons, applications, employers; AI extraction; Excel + advanced PDF export. |
| [`formbro-fill`](./skills/formbro-fill/SKILL.md) | **PDF entry point for agents** — fill IRCC IMM PDFs (IMM0008 / IMM5257 / IMM5645 / IMM5709 / etc.). Auto-detects TR vs PR; rejects LMIA. Single stable command (`formbro fill`) regardless of backend-vs-local engine. |
| [`formbro-webform`](./skills/formbro-webform/SKILL.md) | **LOCAL MODE** Playwright IRCC / Service Canada portal fills — runs on the user's machine. Agent prepares; user submits. |

## The only thing the user inputs

A FormBro API token (starts with `fb_…`).

The user generates it once at https://formbro.ca → Settings → API Tokens. The `connect-formbro` skill then runs `formbro login --token <token>`, which writes `~/.formbro/config.json` (or `%USERPROFILE%\.formbro\config.json` on Windows). Every other skill reads from that config — the token is never asked for again, never echoed, and never stored anywhere except that single file.

## Token safety (enforced by skill text)

- Token is **never** printed. Skills mask any `fb_*` value as `fb_***`.
- Token is **never** written into any file other than the CLI's own `config.json`.
- Token is **never** transmitted to anything other than `https://backend.formbro.ca` (the value baked into `runtime-manifest.json`).

## Per-platform binary selection

`runtime-manifest.json` declares:

| Platform key | Binary path |
|---|---|
| `darwin-arm64` | `bin/darwin-arm64/formbro` |
| `darwin-x64` | `bin/darwin-x64/formbro` |
| `win32-x64` | `bin/win32-x64/formbro.exe` |

Each skill resolves `${process.platform}-${process.arch}` against `binary.platforms`, verifies the matching `.sha256` sidecar, marks the file executable on POSIX, then shells out to it.

## Install

See the repo-root [README](../../README.md) for `codex plugin marketplace add` / `codex plugin install` commands.

## Versioning

The plugin's `version` (in `.codex-plugin/plugin.json`) is **independent** of the FormBro product semver. Plugin bumps follow plugin-schema needs, not FormBro release cadence.

## What this plugin is NOT

- Not an MCP server. (For MCP, see `https://mcp.formbro.ca` or the upstream `formbro-mcp-server` npm package.)
- Not a Claude Desktop / Claude Code adapter — Codex App only.
- Not a re-implementation of FormBro logic — every skill shells out to `formbro <subcommand>`.
- Not a place to store credentials in source.
