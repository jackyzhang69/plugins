# anychat (public plugin)

Published under `jackyzhang69/plugins` → `plugins/anychat/`.

**Ships:** skills, install metadata, and prebuilt product components.

**Does not ship:** user data or private development materials.

## Skills

One discovery file: `skills/anychat/SKILL.md`. Playbooks live under
`skills/anychat/references/` (connect, setup, query, media, export, tell-jacky).

## Platforms and stable sources

| Platform | Stable source scope |
|----------|---------------------|
| macOS Apple Silicon | Explicitly verified WeChat profiles + iMessage + Telegram Postbox profile |
| Windows x64 | Explicitly verified WeChat/Weixin profiles only; Telegram, Signal, and WhatsApp remain behind later native gates |

Unknown chat-app builds fail closed. `setup_plan.agent` is the only first-time-setup protocol for host agents: say `say_to_user` to the human, run `host_agent_args` yourself, and never send the human to a terminal. A unique running WeChat/Weixin build is selected automatically. Invented or stale confirmed builds are rejected. Direct first-time preparation is limited to 4.1.0–4.1.10 (Windows: through 4.1.10.30). Missing or newer clients get `setup_plan.recommended_installer` (macOS 4.1.5 / Windows 4.1.9 on the public S3 slot); the human installs, the agent does not. Archive readability, first-time acquisition, schema, and media remain separately verified per exact profile.

See [`RELEASE_NOTES.md`](./RELEASE_NOTES.md) for the current release changes and package coverage.

Dev (macOS): `../scripts/install-local.sh` → `~/.local/bin/anychat` + `~/.local/bin/anychat-access`.  
Stage both platforms (writes manifest + sidecars): `scripts/stage-package` (repo-level dev tool).  
Verify staged package consistency (read-only gate): `plugin/scripts/verify-package`.

Connect (secure): `printf %s "$TOKEN" | anychat login --token-stdin --accept-personal-use`.  
Tell Jacky: `anychat feedback create … --user-confirmed` (Portal + local mirror fallback).

## Talk to the human

Host agents must follow **anychat → Talk to the human** (and
platform-vault `delivery/plugin-policy.md`): plain product language, major
stages only—do not lead the chat with paths, `--help`, or raw JSON.
