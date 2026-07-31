---
name: anychat-setup
description: >-
  First-run wizard for local chat archive access on this machine. Run after
  connect-anychat when doctor reports setup_needed.
when_to_use: |-
  doctor says setup_needed / archive not ready / first query fails with setup incomplete.
---

# AnyChat setup

## Goal

Enable local archive access for **this user on this computer**. Keep instructions
novice-friendly. Do not explain internal mechanisms.

## Talk to the human

Report only major stages (need chat app logged in → one-time prepare-access → self-check names → ready). Do not dump raw `doctor --json` into chat; say “还没准备好本机档案 / 已经可以用了”. Never mention keys, databases, or helper binary names. Follow `anychat-capabilities` § **Talk to the human**.

## Steps

1. `anychat doctor --json` — note readiness flags for *your* decision; speak product language to the user.
2. Ensure the chat app is **installed and logged in** on this computer (macOS or Windows).
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
6. If setup fails with a product code (`E_SETUP_*`):
   - Tell the user in plain language; optionally offer **tell-jacky** (redacted).
7. On success, self-check:

```bash
"$ANYCHAT_BIN" friends list --limit 5 --json
```

   Show sample display names; ask: “这是你的账号吗？”  
   Only after confirm, proceed to queries.

8. Demo value (optional): `anychat recents` or a short friend query.

## macOS / Windows notes (user-facing)

- Chat app must stay open and logged in during first-time access.
- OS may ask to open a helper or approve Administrator once — user does that themselves.
- Do not invent hex keys, DB paths, or internal filenames for the user.

## Never

- Ask the user for hex keys or database paths.
- Run destructive deletes.
- Upload chat content during setup.
- Explain how access works (encryption, process scan, etc.).
