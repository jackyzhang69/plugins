---
name: anychat-export
description: >-
  Export scoped chat transcripts (text/json/md) and attachment packs.
  Same strict modes as anychat-query. Content stays local.
when_to_use: |-
  "export my chat with X", "save group transcript", "download all images from …".
---

# AnyChat export

## Advisory marketplace check

On first AnyChat use in each host-agent session, run `"$ANYCHAT_BIN" doctor --check-upgrade --json` before the requested action. If `upgrade.status` is `update_available`, briefly recommend updating from the marketplace, then continue normally. Never auto-update, never block the user, and continue silently when the check is unavailable. Run this once per session load, not before every command.

Resolve `$ANYCHAT_BIN` via **anychat-capabilities** §B. Requires connect + setup.

## Transcript

```bash
# JSON (default for export) — good for agents
"$ANYCHAT_BIN" export --mode friend --target "<name>" --days 30 \
  -o ./anychat-export/messages.json

# Markdown / text via query
"$ANYCHAT_BIN" query --mode group --target "<group>" --days 7 \
  --format md -o ./anychat-export/group.md
```

## Attachments pack

```bash
"$ANYCHAT_BIN" media download-all --mode friend --target "<name>" \
  --type image --days 30 -o ./anychat-export/images
```

Voice: download yields **playable WAV**; **agent STT only** (no anychat AI).

## Rules

1. Always declare `--mode` (never mix friend + group).
2. Prefer `resolve --json` when the name is ambiguous.
3. Do not upload export folders to Portal or third parties unless the user asks.
