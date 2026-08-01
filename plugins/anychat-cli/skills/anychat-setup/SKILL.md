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

Report only major stages (need chat app logged in → one-time prepare-access → self-check names → ready). Do not dump raw `doctor --json` into chat; say “还没准备好本机档案 / 已经可以用了”. Never mention keys, databases, or helper binary names. Follow `anychat-capabilities` § **Talk to the human**.

## Steps

1. `anychat doctor --json` — note readiness flags for *your* decision; speak product language to the user.
2. Ensure the archive comes from **WeChat/Weixin 4.1 or newer** and the chat app is logged in on this computer. Older archive versions are not supported; stop and ask the user to update. On Windows, keep the **main** WeChat/Weixin window open and fully signed in (helper processes alone are not enough). Automatic first-time access may still be unavailable for a particular OS/app build or local security posture; report `E_LOCAL_ACCESS_METHOD_UNAVAILABLE` honestly rather than saying the app is older or unsupported, and offer one retry after a normal re-login.
3. If this OS is not macOS/Windows, stop and explain not supported yet.
4. Run:

```bash
"$ANYCHAT_BIN" setup --yes
```

5. If setup says on-device access is not ready:

```bash
"$ANYCHAT_BIN" prepare-access
"$ANYCHAT_BIN" setup --yes
```

   - User may need to approve one elevated step / Mac password (agents never collect passwords).
6. If setup / prepare-access fails with a product code (`E_SETUP_*`, `E_LOCAL_*`, `E_CHAT_*`, or `E_WINDOWS_*`):
   - Tell the user in plain language.
   - When automatic access is unavailable (`E_LOCAL_ACCESS_METHOD_UNAVAILABLE`, timeout, or budget), the CLI already saved a **redacted access diagnosis** and printed how to send it.
   - Offer **tell-jacky** once: diagnosis attaches automatically on `feedback create` (user still confirms the draft). Do not ask the user to invent technical fields.
7. On success, self-check:

```bash
"$ANYCHAT_BIN" friends list --limit 5 --json
```

   Show sample display names; ask: “这是你的账号吗？”  
   Only after confirm, proceed to queries.

8. Demo value (optional): `anychat recents` or a short friend query.

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
