# AnyKnow

Private personal knowledge with confirmed changes, search, browsing, and portable export.

Platforms: macOS Apple Silicon (`darwin-arm64`), Windows x64 (`win32-x64`).

Load [`skills/anyknow/SKILL.md`](skills/anyknow/SKILL.md) first. Connect and product verbs are defined there and under `skills/anyknow/references/`.

This package is an **Agent Skills** tree plus a native CLI. Any agent that can attach a skill folder or this full plugin directory can use it — marketplace `plugin install` is optional convenience, not a requirement.

- Full tree (preferred): this directory, including `bin/` and `runtime-manifest.json`.
- Skill-only attach: only after `~/.jackyzhang.app/plugins/anyknow/current` already holds the matching CLI.

Read `runtime-manifest.json` for the exact package version and platform checksums.
