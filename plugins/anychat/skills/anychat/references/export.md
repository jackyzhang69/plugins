# AnyChat export

Resolve `$ANYCHAT_BIN` via the product router §B. Requires connect + setup.

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

Voice: download yields **playable WAV**; **this host agent transcribes** (AnyChat has no speech-to-text; that is intentional).

## Rules

1. Always declare `--mode` (never mix friend + group).
2. Prefer `resolve --json` when the name is ambiguous.
3. Do not upload export folders to Portal or third parties unless the user asks.
