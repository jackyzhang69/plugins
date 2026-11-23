<!-- generated-by: render_public_plugin_docs.py ; do not edit -->
# jacky-plugins

Official Jacky plugins for agents that speak **Agent Skills** (and for hosts with a plugin marketplace).

One skill tree per product under `plugins/<plugin_id>/skills/`. The native CLI lives beside it under `bin/`. Marketplace install commands are convenience; any agent that can attach a `SKILL.md` folder or the full plugin tree can use the same package.

## Available Plugins

| Plugin | Version | Platforms | Description |
|---|---|---|---|
| [`formbro`](./plugins/formbro) | 1.9.11 | macOS (`arm64`), Windows (`x64`), Linux (`x64`) | Canadian immigration form automation: import cases via JSON contracts, validate against IRCC schemas, generate filled IMM PDFs, and drive local browser webform fills (stops safely before submit). |
| [`easybooks`](./plugins/easybooks) | 0.5.28 | macOS (`arm64`), Windows (`x64`), Linux (`x64`) | Bookkeeping for self-employed Canadians via the bundled EasyBooks CLI: drop a receipt, invoice, or scan Gmail and have it recorded into EasyBooks. The CLI is the only boundary for all EasyBooks reads and writes — record income/expenses, create and send invoices, and resolve clients/categories. |
| [`anychat`](./plugins/anychat) | 0.1.105 | macOS (`arm64`), Windows (`x64`), Linux (`x64`) | Search and export your own local chat archive via the AnyChat CLI. Answers 'what can AnyChat do' from the anychat router; Tell Jacky feedback goes to Portal after user confirm. |
| [`anycase`](./plugins/anycase) | 1.3.1 | macOS (`arm64`), Windows (`x64`), Linux (`x64`) | Canadian Immigration Intelligence via the anycase router: Federal Court precedents, IRCC policy & Q&A, and CLB statutory calculators. |
| [`anydoc`](./plugins/anydoc) | 0.3.16 | macOS (`arm64`), Windows (`x64`), Linux (`x64`) | Inspect a messy local document folder and assemble an approved upload pack via the bundled AnyDoc CLI. Offline packing. Optional Portal token for Tell Jacky. macOS Apple Silicon and Windows x64. |
| [`anyweb`](./plugins/anyweb) | 0.2.30 | macOS (`arm64`), Windows (`x64`), Linux (`x64`) | Fill a supported Express Entry profile on this computer up to the last review before submit, and optionally save a website login for reuse here. |
| [`anyknow`](./plugins/anyknow) | 0.1.22 | macOS (`arm64`), Windows (`x64`), Linux (`x64`) | Private knowledge preparation, confirmed storage, search, browsing, export, feedback, and support pairing. |
| [`anymail`](./plugins/anymail) | 0.1.12 | macOS (`arm64`), Windows (`x64`), Linux (`x64`) | Agent-native Gmail and Microsoft mail client. Load the anymail router; ask the live CLI, never invent providers or secrets. |
| [`anywrite`](./plugins/anywrite) | 0.1.9 | macOS (`arm64`), Windows (`x64`), Linux (`x64`) | Teach the assistant how you write a kind of document, keep confirmed writing methods, and write new documents in your style. |
| [`anypdf`](./plugins/anypdf) | 0.7.36 | macOS (`arm64`), Windows (`x64`), Linux (`x64`) | Secure agent-native PDF form filling, one-shot intake, and Tell-Jacky feedback. Load the anypdf router and confirm current support with `anypdf forms catalog`, including IMM0008; never promise support from a frozen form list. New-template intake accepts only the issuing authority's official blank PDF; `new_source` does not mean published or fill-ready. Do not guess unconfirmed facts or submit an application to an issuer on the user's behalf. IRCC PDFs still requiring Acrobat Validate are not ready for upload. |

## Installation

### Hosts with a plugin marketplace

#### Claude Code

```bash
claude plugin marketplace add jackyzhang69/plugins --sparse .claude-plugin plugins/formbro
claude plugin install formbro@jacky-plugins
```

Replace `formbro` with the plugin id from the table (`anypdf`, `anyweb`, …).

#### Codex CLI / Codex App

```bash
codex plugin marketplace add jackyzhang69/plugins --sparse .agents/plugins --sparse plugins/formbro
codex plugin add formbro@jacky-plugins
```

Replace `formbro` with the plugin id from the table (`anypdf`, `anyweb`, …).

Marketplace installs must use --sparse. The repository carries every plugin's native binaries (a plain checkout is about 300 MB, the full history over 1 GB), and Codex clones the whole history under a 30 second limit, so a plain add fails. --sparse fetches only the catalog and the plugins you list, so pass every plugin you want. Narrow each listed plugin to this host's `bin/<os-arch>` directory: listing whole `plugins/<plugin_id>` packages still pulls all three OS binaries and times out (measured 2026-11-21: 13 s per-OS versus 44 s full, on host 69 and Jacky's Mac). To add one later, append the plugin's subdirectories plus its `bin/<os-arch>` to the marketplace's sparse paths instead of removing the marketplace (removing it uninstalls its plugins). Codex - sparse_paths of jacky-plugins in ~/.codex/config.toml, then codex plugin marketplace upgrade jacky-plugins. Claude Code - sparsePaths of jacky-plugins in both settings.json (extraKnownMarketplaces) and plugins/known_marketplaces.json, then claude plugin marketplace update jacky-plugins.

### Any Agent Skills–compatible agent

Cherry Studio, Cursor, Gemini CLI, and other Agent Skills hosts do **not** need a Jacky-specific `plugin install` command. Attach the package the host already understands:

- Clone or open https://github.com/jackyzhang69/plugins and attach plugins/<plugin_id>/ (full tree: skills + bin + runtime-manifest).
- Or load only skills/<plugin_id>/ after the matching CLI already exists at ~/.jackyzhang.app/plugins/<plugin_id>/current.
- From the package root, run bin/<platform>/<plugin_id> doctor --repair-install once, then use the canonical CLI path for every later command.

After attach, load `skills/<plugin_id>/SKILL.md`. Product commands use the repaired CLI under `~/.jackyzhang.app/plugins/<plugin_id>/current`, never whatever binary happens to be first on `PATH`.

## Before first use — platform prerequisites

Read the product's `skills/<plugin_id>/SKILL.md` first. Two platform prerequisites account for most "the plugin is broken" reports:

- **macOS — Full Disk Access belongs to the app that runs your agent, not to the plugin binary.** Products that read data kept by another app (AnyChat reads the chat archive) are credited by macOS to the host app that launched the CLI. System Settings → Privacy & Security → Full Disk Access → add that agent host (DeepSeek Harness, Claude, ChatGPT, Codex, Cherry Studio, …) → quit and reopen it. Granting the bundled `anychat` binary itself does not work.
- **Windows — one supported source-app build, permission prompt allowed, source app signed in.** Install only the build the product lists, approve the UAC prompt when setup asks, and keep the source app signed in and running during setup. If setup reports a key/permission failure, collect the product's `doctor --json` and send it through Tell Jacky; do not reinstall the source app and do not delete chat data.

Update the product CLI before diagnosing — for AnyChat: `anychat update --json`, then `anychat doctor --json`. Current releases refresh a stale local authorization cache during upgrade.

## Package shape

Each plugin under `plugins/<plugin_id>/` contains:

- `.claude-plugin/plugin.json` and `.codex-plugin/plugin.json` — host marketplace metadata when that host uses a catalog
- `skills/<plugin_id>/SKILL.md` — the single Agent Skills router; playbooks live in `references/`
- `bin/<platform>/` — native CLI for `darwin-arm64` and `win32-x64`, with `.sha256` sidecars
- `runtime-manifest.json` — platform map and package verification
- `README.md` — thin package card (generated; details live in the skill)

## Hard rules (user-facing)

- **One Portal sign-in.** Create a durable user token at [jackyzhang.app/account/tokens](https://jackyzhang.app/account/tokens). The host agent connects for you; the token is piped on stdin (`login --token-stdin`), never placed on the command line.
- **Shared credential slot.** Successful connect writes mode-`0600` `~/.jackyzhang.app/token/user.json` (Windows: `%USERPROFILE%\.jackyzhang.app\token\user.json`). Other official plugins reuse that slot.
- **Local native CLI.** Skills drive the bundled binary. Documents and site sessions that the product keeps local stay on this machine except for the official product backends the skill names.
- **No secrets in chat artifacts.** Do not paste tokens into skill text, logs, screenshots, or argv.

## License

MIT (see [LICENSE](./LICENSE)).

<!-- end generated-by: render_public_plugin_docs.py -->
