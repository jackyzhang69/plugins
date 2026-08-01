---
name: anychat-media
description: >-
  List and download attachments (image, voice, video, file) from the local chat
  archive under the same strict modes as query.
when_to_use: |-
  "download images from chat with X", "export that PDF", "list voice messages", "attachments in group Y".
---

# AnyChat media

## Advisory marketplace check

On first AnyChat use in each host-agent session, run `"$ANYCHAT_BIN" doctor --check-upgrade --json` before the requested action. If `upgrade.status` is `update_available`, briefly recommend updating from the marketplace, then continue normally. Never auto-update, never block the user, and continue silently when the check is unavailable. Run this once per session load, not before every command.

Same `--mode` rules as query. Never scan all media without a mode/target.

```bash
# List
"$ANYCHAT_BIN" media list \
  --mode friend --target "<name>" \
  --type image|voice|video|file|all \
  --days 90 --limit 50 --json

# Links / mini-programs (A6) — download writes JSON sidecar (title+url), no crawl
"$ANYCHAT_BIN" media list --mode friend --target "<name>" --type link --days 90 --json
"$ANYCHAT_BIN" media download --id "<id>" -o ./anychat-export --json

# Videos (A5) — local mp4 when cached; missing if never downloaded on this machine
"$ANYCHAT_BIN" media list --mode group --target "<group>" --type video --days 90 --json
"$ANYCHAT_BIN" media download --id "<id>" -o ./anychat-export --json

# Download one (id from list) — use --json for playable_path (agent STT input)
"$ANYCHAT_BIN" media download --id "<id>" -o ./anychat-export --json

# Batch
"$ANYCHAT_BIN" media download-all \
  --mode friend --target "<name>" --type image \
  --days 90 --limit 100 -o ./anychat-export
```

## Status

- `present` — file copied or available  
- `missing` — message exists, local file not found (report honestly; do not invent)

## Voice (playable + text extraction)

1. **Playable audio:** `media download` / `download-all` for `type=voice` writes a standard **WAV** (not proprietary raw).
2. **Text / transcript:** AnyChat does **not** provide AI/STT. After download, the **user agent** runs their own speech-to-text on the WAV path (local Whisper, cloud STT, etc.).
3. List JSON includes `playable_format` (`wav` when present), `id`, and top-level `agent_stt` contract text.
4. Missing local voice blobs report `status=missing` (no silent success).

```bash
# Example agent flow for transcript
"$ANYCHAT_BIN" media list --mode friend --target "<name>" --type voice --days 90 --json
"$ANYCHAT_BIN" media download --id "<id>" -o ./anychat-export
# then: agent STT on ./anychat-export/voice_*.wav  →  show text to user
```

## Notes

- Images/files may be proprietary container formats (e.g. `.dat`); still download as stored.
- For person-across-groups attachments, use matching mode (`person-in-groups`).
