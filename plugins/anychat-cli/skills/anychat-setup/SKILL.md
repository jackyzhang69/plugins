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

Enable local archive access for **this user on this computer**. Keep instructions
novice-friendly. Do not explain internal mechanisms.

## Talk to the human

Report only major stages (need chat app logged in → choose a version path when required → one-time local preparation → self-check names → ready). Do not dump raw `doctor --json` into chat; say “还没准备好本机档案 / 已经可以用了”. Never mention keys, databases, or helper binary names. Follow `anychat-capabilities` § **Talk to the human**.

## Steps

1. Run `anychat doctor --json`. Treat its `setup_plan` as the only setup-routing authority; do not compare versions yourself.
2. If this OS is not macOS/Windows, stop and explain not supported yet. On Windows, keep the **main** WeChat/Weixin window open and fully signed in (helper processes alone are not enough).
3. Route exactly by `setup_plan.state`:

   - `ready` — run `anychat setup --yes`, then continue to the self-check.
   - `prepare_supported` — the detected version is in the direct first-time range. Run the preparation sequence below without asking the user to choose a version path.
   - `version_choice_required` — do **not** run `prepare-access`. Show the two `setup_plan.choices` by translating each choice's `customer_message` into the user's language; identify the choice whose `recommended` field is true. After the user chooses, follow that choice's `host_agent_steps` exactly. Never install, uninstall, downgrade, or upgrade WeChat/Weixin silently.
   - `client_upgrade_required` — ask the user to update WeChat/Weixin into the 4.1.0–4.1.10 range, then rerun `doctor --json`.
   - `version_confirmation_required` — show the detected versions from `setup_plan.version_confirmation_choices`. After the user identifies the version to use, run that choice's exact `host_agent_args` (`anychat setup --confirm-wechat-build <exact-build>`), then rerun `doctor --json`. The CLI records the confirmation and validates it against fresh detection; never guess or invent a build string.

4. Direct preparation sequence (`prepare_supported` only):

```bash
"$ANYCHAT_BIN" prepare-access
"$ANYCHAT_BIN" setup --yes
```

   The user may need to approve one elevated step / Mac password; agents never collect passwords.
5. If setup / prepare-access fails with a product code (`E_SETUP_*`, `E_LOCAL_*`, `E_CHAT_*`, `E_WECHAT_*`, or `E_WINDOWS_*`):
   - Tell the user in plain language.
   - For `E_WECHAT_VERSION_CHOICE_REQUIRED`, rerun `doctor --json` and use its two choices; do not offer a retry loop.
   - For a low-version method failure (`E_LOCAL_ACCESS_METHOD_UNAVAILABLE`, timeout, or budget), the CLI already saved a **redacted access diagnosis** and printed how to send it.
   - Offer **tell-jacky** once: diagnosis attaches automatically on `feedback create` (user still confirms the draft). Do not ask the user to invent technical fields.
6. On success, self-check:

```bash
"$ANYCHAT_BIN" friends list --limit 5 --json
```

   Show sample display names; ask: “这是你的账号吗？”  
   Only after confirm, proceed to queries.

7. Demo value (optional): `anychat recents` or a short friend query.

## macOS / Windows notes (user-facing)

- Chat app must stay open and logged in during first-time access.
- Windows often runs several WeChat/Weixin processes; the signed-in main window is what matters.
- OS may ask the user to approve one local-access step; the user does that themselves.
- Do not invent or expose internal access material, local storage paths, or component filenames.

## Never

- Ask the user for internal access material or local storage paths.
- Run destructive deletes.
- Upload chat content during setup.
- Explain internal access implementation details.
