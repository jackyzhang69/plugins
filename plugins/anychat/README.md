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
| macOS Apple Silicon | Verified local social-app archives on this computer |
| Windows x64 | Verified local social-app archives on this computer; other apps stay behind later native gates |

Unknown chat-app builds fail closed. First-time setup is `"$ANYCHAT_BIN" provision --json`: speak `say_to_user`, wait on `needs_human`, then run `continue_args`. Invented or stale confirmed builds are rejected. Missing or newer clients get a pinned supported installer; after the human agrees, the CLI overwrites the app only. Archive readability, first-time acquisition, schema, and media remain separately verified per exact profile.

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
