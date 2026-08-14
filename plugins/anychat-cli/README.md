# anychat-cli (public plugin)

Published under `jackyzhang69/plugins` → `plugins/anychat-cli/`.

**Ships:** skills, install metadata, and prebuilt product components.

**Does not ship:** user data or private development materials.

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
| macOS Apple Silicon | AnyChat + bundled on-device access component |
| Windows x64 | AnyChat + bundled on-device access component |

Supported archive family: **WeChat/Weixin 4.1 or newer**. Automatic first-time access depends on the OS and chat-app build.

Dev (macOS): `../scripts/install-local.sh` → `~/.local/bin/anychat` + `~/.local/bin/anychat-access`.  
Stage both platforms (writes manifest + sidecars): `scripts/stage-package` (repo-level dev tool).  
Verify staged package consistency (read-only gate): `plugin/scripts/verify-package`.

Connect (secure): `printf %s "$TOKEN" | anychat login --token-stdin --accept-personal-use`.  
Tell Jacky: `anychat feedback create … --user-confirmed` (Portal + local mirror fallback).

## Talk to the human

Host agents must follow **anychat-capabilities → Talk to the human** (and
platform-vault `delivery/plugin-policy.md`): plain product language, major
stages only—do not lead the chat with paths, `--help`, or raw JSON.
