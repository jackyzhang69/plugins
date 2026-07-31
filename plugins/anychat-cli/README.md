# anychat-cli (public plugin)

Published under `jackyzhang69/plugins` → `plugins/anychat-cli/`.

**Ships:** skills + runtime-manifest + prebuilt binaries.  
**Does not ship:** source, keys, decrypt notes, research trees.

## Skills

| Skill | Role |
|-------|------|
| `connect-anychat` | Portal token once |
| `anychat-capabilities` | Intent → CLI router (every session) |
| `anychat-setup` | First-run local archive access |
| `anychat-query` | Friend / group / search queries |
| `anychat-media` | Attachment list / download |
| `tell-jacky` | Feedback (confirm first) |

## Platforms

| Platform | Binary |
|----------|--------|
| macOS Apple Silicon | `bin/darwin-arm64/anychat` (+ optional `access_scan`) |
| Windows x64 | `bin/win32-x64/anychat.exe` + `access_scan.exe` |

Dev (macOS): `../scripts/install-local.sh` → `~/.local/bin/anychat`.  
Package both platforms: `plugin/scripts/verify-install`.

Connect (secure): `printf %s "$TOKEN" | anychat login --token-stdin --accept-personal-use`.  
Tell Jacky: `anychat feedback create … --user-confirmed` (Portal + local mirror fallback).

## Talk to the human

Host agents must follow **anychat-capabilities → Talk to the human** (and
platform-vault `delivery/plugin-policy.md`): plain product language, major
stages only—do not lead the chat with paths, `--help`, or raw JSON.
