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

Report only major stages (need chat app logged in → need one elevated helper step → self-check names → ready). Do not dump raw `doctor --json` into chat; say “还没准备好本机档案 / 已经可以用了”. Follow `anychat-capabilities` § **Talk to the human**.

## Steps

1. `anychat doctor --json` — note `wechat_running`, `archive_ready`, `setup_complete`, `os`.
2. Ensure the chat app is **installed and logged in** on this computer (macOS or Windows).
3. If status is `setup_unsupported` (non macOS/Windows), stop and explain this platform is not ready.
4. Run:

```bash
"$ANYCHAT_BIN" setup --yes
```

5. If setup prints an elevated helper command:
   - **macOS:** user pastes into Terminal and types their Mac password (agents never collect passwords).
   - **Windows:** user pastes into **Administrator** PowerShell / cmd (agents never collect admin passwords).
6. If setup fails with a product support code:
   - Show the code to the user.
   - Offer **tell-jacky** bug report (redacted).
7. On success, run a **self-check**:

```bash
"$ANYCHAT_BIN" friends list --limit 5 --json
```

   Show sample display names; ask: “这是你的账号吗？”  
   Only after confirm, proceed to queries.

8. Demo value (optional):

```bash
"$ANYCHAT_BIN" recents
# or a short friend query if user names someone
```

## macOS notes (user-facing)

- If the OS blocks a helper app: **System Settings → Privacy & Security → Open Anyway**.
- If a Terminal line is printed for admin approval: user pastes it and types **their Mac password** (agents never collect passwords).
- May require the chat app running and logged in.

## Windows notes (user-facing)

- Chat app must be **running and logged in** during setup.
- If SmartScreen blocks the helper: **More info → Run anyway**.
- Elevated helper: paste the printed line into **Administrator PowerShell**.
- Binary path: plugin `bin/win32-x64/anychat.exe` (or `$ANYCHAT_BIN`).

## Never

- Ask the user for hex keys or database paths.
- Run destructive deletes.
- Upload chat content during setup.
