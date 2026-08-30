# AnyChat media

## Advisory marketplace check

On first AnyChat use in each host-agent session, run `"$ANYCHAT_BIN" doctor --check-upgrade --json` before the requested action. If `upgrade.status` is `update_available`, briefly recommend updating, then continue normally. Never auto-update, never block the user, and continue silently when the check is unavailable. Run this once per session load, not before every command. Updates remain an explicit host-plugin-manager action.

Same `--mode` rules as query. Never scan all media without a mode/target.

Run the intended media command first. If it returns a typed readiness envelope,
follow only its `continue_args` and [setup](setup.md). Keep `resume_token` out of
chat and argv. When ready, pipe it over stdin to the returned continuation;
AnyChat resumes the exact original media request without another human turn.

```bash
# List
"$ANYCHAT_BIN" media list \
  --mode friend --target "<name>" \
  --type image|voice|video|file|all \
  --days 90 --limit 50 --json

# Links and mini-programs
"$ANYCHAT_BIN" media list --mode friend --target "<name>" --type link --days 90 --json
"$ANYCHAT_BIN" media download --id "<id>" -o ./anychat-export --json

# Videos
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

- `present` — at least one local media blob exists; download still verifies that an image can be converted into a normal raster
- `missing` — message exists, local file not found (report honestly; do not invent)

## Voice (playable + text extraction)

1. **Playable audio:** `media download` / `download-all` for `type=voice` writes a standard **WAV**.
2. **Text / transcript:** AnyChat does **not** provide AI/STT. After download, the **user agent** runs their own speech-to-text on the WAV path (local Whisper, cloud STT, etc.).
3. List JSON includes `playable_format` (`wav` when present), `id`, and top-level `agent_stt` contract text.
4. Missing local voice blobs report `status=missing` (no silent success).

```bash
# Example agent flow for transcript
"$ANYCHAT_BIN" media list --mode friend --target "<name>" --type voice --days 90 --json
"$ANYCHAT_BIN" media download --id "<id>" -o ./anychat-export
# then: agent STT on ./anychat-export/voice_*.wav  →  show text to user
```

## Images

1. `media download` / `download-all` writes a normal JPEG, PNG, GIF, or WebP.
2. If a local image cannot be opened safely, AnyChat returns
   `E_IMAGE_NOT_PREVIEWABLE` instead of claiming success.
3. The agent must not upload an unreadable local file as a workaround.

## Notes

- Non-image files are copied in their stored format.
- For person-across-groups attachments, use matching mode (`person-in-groups`).
