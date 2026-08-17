---
name: anychat-setup
description: >-
  First-run wizard for local chat archive access on this machine. Run after
  connect-anychat when doctor reports setup_needed.
when_to_use: |-
  doctor says setup_needed / archive not ready / first query fails with setup incomplete.
---

# AnyChat setup

## Advisory marketplace check

On first AnyChat use in each host-agent session, run `"$ANYCHAT_BIN" doctor --check-upgrade --json` before the requested action. If `upgrade.status` is `update_available`, briefly recommend updating from the marketplace, then continue normally. Never auto-update, never block the user, and continue silently when the check is unavailable. Run this once per session load, not before every command.

## Goal

Enable local archive access for **this user on this computer**. The human
never uses a terminal. You absorb every problem.

## Host-agent contract (mandatory)

1. The human **never uses a terminal**. Never say “run”, “命令”, “Terminal”, or paste CLI to the human.
2. `doctor --json` → `setup_plan` is the **only setup-routing authority**. Follow `setup_plan.agent` exactly. Do not compare WeChat versions yourself.
3. Speak only `setup_plan.agent.say_to_user` (translate if needed). The only clicks you may ask for are `setup_plan.agent.human_os_actions`.
4. You run every CLI argv in `setup_plan.agent.host_agent_args` / `host_agent_steps`. If something fails, re-read `doctor --json` and follow the new `setup_plan.agent`. Do not invent a retry loop.
5. If `offer_tell_jacky` is true: draft Tell Jacky yourself (diagnosis attaches), show the draft, send only after `--user-confirmed`. Never ask the human to invent technical fields.
6. If `do_not_retry_prepare` is true: do not run `prepare-access` again.
7. Never install, uninstall, downgrade, or upgrade WeChat/Weixin unless the human independently offers. The recommended path is that you handle it.

## Talk to the human

Report only major stages (need the chat app open → one password/Open Anyway click if asked → ready). Say “还没准备好本机档案 / 已经可以用了”. Never mention keys, databases, helper names, version numbers, or paths. Follow `anychat-capabilities` § **Talk to the human**.

## Steps

1. Run `anychat doctor --json`. Treat `setup_plan` (especially `setup_plan.agent`) as the only setup-routing authority.
2. If this OS is not macOS/Windows, stop and explain it is not supported yet. Do not ask the human to switch computers.
3. Route exactly by `setup_plan.state`:

   - `ready` — run `anychat setup --yes`, then the self-check. Say the archive is ready.
   - `prepare_supported` — run `anychat prepare-access`, then `anychat setup --yes`. The human may see one password or Administrator window; you never collect the password.
   - `chat_app_not_running` — say `setup_plan.agent.say_to_user`. Wait until they open WeChat. Re-run `doctor --json`. Do not ask for a version number.
   - `running_build_mismatch` — say `setup_plan.agent.say_to_user`. They quit the extra WeChat from the menu bar/tray (closing the window is not enough). Re-run `doctor --json`. Do not run `prepare-access`.
   - `version_confirmation_required` — do **not** run `prepare-access`. Ask them to leave only the WeChat they chat with open. Re-run `doctor --json`. The CLI auto-selects a unique running build. Only if the state is still `version_confirmation_required` after that, run one `setup_plan.version_confirmation_choices` entry's exact `host_agent_args` (`anychat setup --confirm-wechat-build <exact-build>`). Never ask them to read a build string off the screen.
   - `version_choice_required` — do **not** run `prepare-access`. Follow the recommended `keep_current_and_report` choice: tell them you are handling it, draft Tell Jacky, confirm once. Do not ask them to change WeChat. The two advanced WeChat-version choices exist only if they independently offer to change WeChat. Never install, uninstall, downgrade, or upgrade WeChat/Weixin silently.
   - `client_upgrade_required` — do not send them to download a specific version. Draft Tell Jacky and confirm once.

4. Direct preparation sequence (`prepare_supported` only):

```bash
"$ANYCHAT_BIN" prepare-access
"$ANYCHAT_BIN" setup --yes
```

   These commands are yours. Never show them to the human.

5. If setup / prepare-access fails with a product code (`E_SETUP_*`, `E_LOCAL_*`, `E_CHAT_*`, `E_WECHAT_*`, or `E_WINDOWS_*`):
   - Re-run `doctor --json` and follow the new `setup_plan.agent`.
   - Tell the human only `say_to_user`.
   - For `E_WECHAT_VERSION_CHOICE_REQUIRED` / `version_choice_required`, do not offer a retry loop.
   - For `E_LOCAL_ACCESS_METHOD_UNAVAILABLE`, timeout, or budget: diagnosis is already saved; offer Tell Jacky once.
   - For `E_MACOS_PERMISSION_REQUIRED` / `E_WINDOWS_PERMISSION_REQUIRED`: one GUI click from `human_os_actions`, then you retry **once**. If it fails again, Tell Jacky. Do not send them hunting through settings.

6. On success, self-check:

```bash
"$ANYCHAT_BIN" friends list --limit 5 --json
```

   Show sample display names; ask: “这是你的账号吗？”
   Only after confirm, proceed to queries.

7. Demo value (optional): `anychat recents` or a short friend query.

## macOS / Windows notes (human-facing)

- Chat app must stay open and logged in during first-time access.
- Extra WeChat windows / helper processes do not count; the signed-in main window does.
- The OS may show one password or “Open Anyway” click. That is the only thing the human does.
- Do not invent or expose internal access material, local storage paths, or component filenames.

## Never

- Ask the human to open Terminal or type a command.
- Ask the human for a WeChat version number, a path, or a log.
- Ask the human for internal access material.
- Run destructive deletes.
- Upload chat content during setup.
- Explain internal access implementation details.
- Retry `prepare-access` in a loop.
