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

## Steps

1. `anychat doctor --json` — note `wechat_running`, `archive_ready`, `setup_complete`.
2. Ensure the chat app is **installed and logged in** on this Mac.
3. If platform is Windows and status is `setup_unsupported`, stop and explain not ready yet.
4. Run:

```bash
"$ANYCHAT_BIN" setup --yes
```

5. If setup fails with a product support code:
   - Show the code to the user.
   - Offer **tell-jacky** bug report (redacted).
6. On success, run a **self-check**:

```bash
"$ANYCHAT_BIN" friends list --limit 5 --json
```

   Show sample display names; ask: “这是你的账号吗？”  
   Only after confirm, proceed to queries.

7. Demo value (optional):

```bash
"$ANYCHAT_BIN" recents
# or a short friend query if user names someone
```

## macOS notes (user-facing)

- If the OS blocks a helper app: **System Settings → Privacy & Security → Open Anyway**.
- If a Terminal line is printed for admin approval: user pastes it and types **their Mac password** (agents never collect passwords).
- May require the chat app running and logged in.

## Never

- Ask the user for hex keys or database paths.
- Run destructive deletes.
- Upload chat content during setup.
