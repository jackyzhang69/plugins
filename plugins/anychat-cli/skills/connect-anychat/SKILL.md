---
name: connect-anychat
description: >-
  One-time setup. Capture the user's free Portal token and persist it via the
  AnyChat CLI (`anychat login --token-stdin`). Run this before any other AnyChat
  skill when ~/.anychat/config.json is missing.
when_to_use: |-
  Trigger phrases:
    - "connect to anychat / set up anychat"
    - "log in to anychat / save my portal token"
    - first AnyChat intent when not logged in
    - bare "anychat" / "what can anychat do" with no config
---

# Connect AnyChat

## Talk to the human

Keep chat plain: “需要先登录一次免费账号” / “登录成功，下一步准备本机档案”. Do not paste `whoami --json` or full CLI help into the reply (use them only as tools). Follow `anychat-capabilities` § **Talk to the human**.

## Already connected

If `anychat whoami` succeeds and entitlement is valid, skip token capture.

## Portal online check

`login` tries Portal HTTPS entitlement first (`POST …/v1/products/anychat/entitlement`).  
If Portal is unreachable, AnyChat uses **offline-grace** (~7d local cache) and still fails closed after expiry.  
Force offline-only: `ANYCHAT_PORTAL_OFFLINE=1`.  
Optional hard revalidation on each query: `ANYCHAT_FORCE_REVALIDATE=1`.
Still run `anychat doctor` when setup may be incomplete.

## First-time path (secure — primary)

1. Ask the user for their **Portal** API token (free AnyChat product — no wallet).
2. Resolve the `anychat` binary (see `anychat-capabilities` §B).
3. Confirm personal-use terms with the user (own data, own machine only).
4. Have the **user** enter the token locally so it never appears in chat/transcripts.
   Preferred CLI path (token on stdin, **not** on argv):

```bash
# User pastes token into a local hidden prompt, or pipes from a local secret store:
printf %s "$TOKEN" | "$ANYCHAT_BIN" login --token-stdin --accept-personal-use
```

Interactive TTY (agent prints the command; user types token themselves):

```bash
"$ANYCHAT_BIN" login --token-stdin --accept-personal-use
```

**Do not** put the raw token into a shell command that will be logged as argv.

Discouraged override (history / process list risk):

```bash
"$ANYCHAT_BIN" login --token "<token>" --accept-personal-use
```

5. Mask any token in logs as `****` / prefix only.
6. Then: `anychat doctor` → if `setup_needed`, load **anychat-setup**.
7. Load **anychat-capabilities** for the session router.

## Token rules

- Never echo the full token.
- Never put the secret on process argv when avoidable (`--token-stdin` first).
- Store only via CLI (`~/.anychat/config.json`).
- Never put a real token value in skill examples.
