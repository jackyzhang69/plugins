<!-- generated-by: render_public_plugin_docs.py ; do not edit -->
# easybooks

Version `0.5.24`.

Bookkeeping for self-employed Canadians via the bundled EasyBooks CLI: drop a receipt, invoice, or scan Gmail and have it recorded into EasyBooks. The CLI is the only boundary for all EasyBooks reads and writes — record income/expenses, create and send invoices, and resolve clients/categories.

Platforms: macOS Apple Silicon (`darwin-arm64`), Windows x64 (`win32-x64`).

Load [`skills/easybooks/SKILL.md`](skills/easybooks/SKILL.md) first. Connect and product verbs are defined there and under `skills/easybooks/references/`.

This package is an **Agent Skills** tree plus a native CLI. Any agent that can attach a skill folder or this full plugin directory can use it — marketplace `plugin install` is optional convenience, not a requirement.

- Full tree (preferred): this directory, including `bin/` and `runtime-manifest.json`, then `bin/<platform>/<cli> doctor --repair-install`.
- Skill-only attach: only after `~/.jackyzhang.app/plugins/easybooks/current` already holds the matching CLI.

What changed in this version: [`skills/easybooks/references/whats-new.md`](skills/easybooks/references/whats-new.md).

<!-- end generated-by: render_public_plugin_docs.py -->
