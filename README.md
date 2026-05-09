# jacky-plugins

Codex App plugins by [Jacky Zhang](https://github.com/jackyzhang69).

## Available plugins

| Name | Version | Description |
|---|---|---|
| [formbro-cli](./plugins/formbro-cli) | 0.1.0 | Canadian immigration form automation (FormBro). Read/write applicants, applications, employers; AI-assisted document extraction; local Playwright-driven IRCC portal fills. The bundled CLI works on macOS Apple Silicon, macOS Intel, and Windows x64. |

## Install in Codex App

### CLI

```bash
# 1. Add this repo as a marketplace (one-time)
codex plugin marketplace add jackyzhang69/plugins

# 2. Install a plugin from it
codex plugin install formbro-cli@jacky-plugins
```

### Desktop App / IDE Extension

1. Settings → Plugins → Marketplace Sources → Add
2. Paste:
   ```
   https://raw.githubusercontent.com/jackyzhang69/plugins/main/.agents/plugins/marketplace.json
   ```
3. Browse the new marketplace, click Install on `formbro-cli`.

## What gets installed

The plugin source is a single directory (`plugins/formbro-cli/`) containing:

- `.codex-plugin/plugin.json` — the Codex App manifest.
- `skills/<name>/SKILL.md` — four skills: `connect-formbro`, `formbro-read`, `formbro-write`, `formbro-webform`.
- `bin/<platform>/formbro[.exe]` — the bundled FormBro CLI for `darwin-arm64`, `darwin-x64`, `win32-x64`. Each binary has a matching `.sha256` sidecar.
- `runtime-manifest.json` — declares per-platform binary paths and config locations so the plugin's skills can resolve the right binary at runtime.
- `README.md` — plugin-level docs (token, install, what each skill does).

## Hard rules across every plugin in this repo

- **The user's only input is whatever credential the plugin needs.** No setup wizards, no provider switches. For FormBro, that's a single API token from formbro.ca.
- **Tokens are never written into source files.** Skills capture the token at runtime through the bundled CLI's own auth path (e.g. for FormBro: `formbro login --token <fb_xxx>` → `~/.formbro/config.json`).
- **Plugins shell out to bundled binaries — they don't reimplement business logic.** Each plugin's `bin/<platform>/` ships the matching tool; skill markdown describes how to invoke it.

## License

MIT (see [LICENSE](./LICENSE)).
