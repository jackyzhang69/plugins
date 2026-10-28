# AnyWrite

Teach the assistant how you write a kind of document, keep the writing methods you confirmed, and write new documents the way you do.

Platforms: macOS Apple Silicon (`darwin-arm64`), Windows x64 (`win32-x64`), Linux x64 (`linux-x64`).

Load [`skills/anywrite/SKILL.md`](skills/anywrite/SKILL.md) first. Flows live under `skills/anywrite/references/`.

This package is an **Agent Skills** tree plus a native CLI. Any agent that can attach a skill folder or this full plugin directory can use it; marketplace install is optional convenience.

- Full tree (preferred): this directory, including `bin/` and `runtime-manifest.json`.
- Skill-only attach: only after the matching CLI is already installed.

Read `runtime-manifest.json` for the exact package version and platform checksums.
