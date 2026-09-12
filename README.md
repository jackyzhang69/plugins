# jacky-plugins

Official plugins for Codex and Claude Code by [Jacky Zhang](https://github.com/jackyzhang69).

## Available Plugins

| Plugin | Version | Platforms | Description |
|---|---|---|---|
| [`formbro`](./plugins/formbro) | 1.9.2 | macOS (`arm64`), Windows (`x64`) | Canadian immigration form automation: import cases via JSON contracts, validate against IRCC schemas, generate filled IMM PDFs, and drive local browser webform fills (stops safely before submit). |
| [`easybooks`](./plugins/easybooks) | 0.5.21 | macOS (`arm64`), Windows (`x64`) | Bookkeeping for self-employed Canadians: ingest receipts/invoices from files or connected Gmail, record income/expenses idempotently, create/send invoices, and manage clients & categories. |
| [`anychat`](./plugins/anychat) | 0.1.80 | macOS (`arm64`), Windows (`x64`) | Personal local chat archive assistant: search messages, export group transcripts, attachments, and voice WAVs. Chat records stay 100% local on your machine. |
| [`anycase`](./plugins/anycase) | 1.1.6 | macOS (`arm64`), Windows (`x64`) | Canadian immigration legal & practical intelligence: Federal Court precedent ratios, IRCC policy manuals & Q&As, practitioner field notes, and statutory CLB language test calculators. |
| [`anydoc`](./plugins/anydoc) | 0.3.8 | macOS (`arm64`), Windows (`x64`) | Local document folder inspector & upload pack assembler: inspect messy client folders, generate approved assembly plans, and assemble immigration document sets offline. |
| [`anyweb`](./plugins/anyweb) | 0.2.17 | macOS (`arm64`), Windows (`x64`) | Headed browser form automation: fills supported Express Entry profiles in visible Chrome on your machine, stopping safely before final submission. Optional saved website login management. |
| [`anypdf`](./plugins/anypdf) | 0.7.15 | macOS (`arm64`), Windows (`x64`) | Secure agent-native PDF form filling: schema-validated intake, deterministic filling of registered PDF templates, and user feedback submission via the bundled AnyPDF CLI. |

## Installation

### 1. Codex CLI / Codex App

```bash
# 1. Add this marketplace (one-time)
codex plugin marketplace add jackyzhang69/plugins

# 2. Install any plugin
codex plugin install formbro@jacky-plugins
```

*(Note: `codex plugin add <plugin>@jacky-plugins` is also supported depending on your Codex CLI version).*

### 2. Claude Code

```bash
# 1. Add this marketplace (one-time)
claude plugin marketplace add jackyzhang69/plugins

# 2. Install any plugin
claude plugin install formbro@jacky-plugins
```

*(Note: You can also pass the full Git URL `https://github.com/jackyzhang69/plugins` if preferred).*

## Package Architecture

Each plugin is self-contained under `plugins/<plugin_id>/`:

- `.claude-plugin/plugin.json` & `.codex-plugin/plugin.json` — manifests defining plugin identity and entry skills for host agents.
- `skills/<plugin_id>/SKILL.md` — the single router and discovery skill for the agent; specialized playbooks reside in `references/`.
- `bin/<platform>/<binary>[.exe]` — prebuilt native CLI binaries for `darwin-arm64` and `win32-x64`, accompanied by `.sha256` checksum sidecars.
- `runtime-manifest.json` — runtime platform-to-binary mapping and package verification rules.
- `README.md` — plugin-specific contract, playbooks, and configuration guidelines.

## Hard Rules & Design Principles

- **Unified Platform Credential**: All official plugins share a single durable Portal token (`jz_…`), generated once at [jackyzhang.app/account/tokens](https://jackyzhang.app/account/tokens).
- **Connect Once**: Authentication is saved to `~/.jackyzhang.app/token/user.json` (mode `0600`; `%USERPROFILE%\.jackyzhang.app\token\user.json` on Windows). Once connected via any official plugin (`<plugin> login --token-stdin`), all other official plugins automatically recognize the session without prompting again.
- **Zero Credential Leaks**: Credentials must never enter shell commands (argv), environment variables, agent chat, or log files. They are piped via stdin and loaded strictly into memory.
- **Local Native Execution**: Plugins execute via bundled native binaries. The agent orchestrates and reads local files, while the CLI handles business logic, deterministic validations, and secure network calls.
- **Local Data Privacy**: Documents, chat databases, and client files stay on the local machine. Network access is restricted to official product backends for validation or synchronized services.

## License

MIT (see [LICENSE](./LICENSE)).
