---
name: connect-anydoc
description: >-
  One-time setup. Capture the user's Portal token and persist it via
  `anydoc login --token-stdin`. Skip if ~/.jackyzhang.app/token/user.json
  already exists from any official plugin. Inspect and explicit manual plans
  stay offline; saved private-model operations require this skill.
when_to_use: |-
  Trigger phrases:
    - "connect to anydoc / log in to anydoc / save my portal token"
    - first Tell-Jacky, whoami, Guides, or private-model intent when not logged in
    - do not run this only to inspect or assemble a folder
---

# Connect AnyDoc

## Shared platform token

- Canonical durable user credential: `~/.jackyzhang.app/token/user.json` (`jz_` only; `credential_kind=user`, `slot=user`).
- **One Portal user token for the whole platform.** FormBro, AnyChat, AnyPDF, AnyWeb, EasyBooks, AnyDoc, and AnyImmi share this file. If it already exists from any official plugin, do **not** ask the human to log in again and do **not** say AnyDoc needs a different Portal token.
- AnyDoc is **exchange mode**: the CLI calls `POST /v1/token/exchange` with `aud=anydoc` and keeps a short-lived JWT in memory. Raw `jz_` is not a product bearer.
- Never print or log the raw token. Confirm with `anydoc doctor --json` (`credential.configured`) or a masked `anydoc whoami --json`.
- Do not create a product-local durable token file. Runtime stays under `~/.jackyzhang.app/anydoc/`. The public package lives at `~/.jackyzhang.app/plugins/anydoc/current`.
- After a marketplace install or update, run `"$PACKAGE_BIN" doctor --repair-install --json` from this plugin package, then use `~/.jackyzhang.app/plugins/anydoc/current/bin/<platform>/anydoc`.

## Already connected

Run:

```bash
"$ANYDOC_BIN" doctor --json
```

If `credential.configured` is true, stop. Do not ask for another token. `login` will return `already_configured` and will not overwrite `user.json` unless the human explicitly asks to replace it (`--force`).

`whoami` may return `unknown_audience` until accountd has `anydoc` deployed. That is an exchange fact, not a reason to collect a second token.

## Manual assemble does not need login

`doctor` / `inspect` and an explicitly approved `manual_plan` work without connect. Resolving, saving, replacing, exporting, or forgetting the user's private assembly model uses accountd and therefore requires connect. Never collect a second token.

## Token delivery (host agent — mandatory)

Same contract as every official plugin (LOCKED 2026-08-24). The host agent performs connect **for** the human. Never tell them to open a terminal and run login themselves.

Accept input in this order:

1. **File containing the token (preferred).** Read the path in the agent tool channel. Pipe stdin only:

```bash
printf %s "$(cat -- "$TOKEN_FILE")" | "$ANYDOC_BIN" login --token-stdin --json
```

2. **Plaintext token in chat (allowed, discouraged).** Warn once that a file path is better. Feed stdin. Do not echo the token back.

3. **No token yet.** Ask for a file path or a paste. Still do not ask them to run terminal commands.

Hard rules:

- `--token <value>` / argv secrets are **forbidden**.
- Never put a real `jz_` in skill text, logs, screenshots, or JSON the human sees.
- After a successful connect, other official plugins must not re-prompt when `user.json` is present.

## Talk to the human

Plain language: “需要先登录一次免费账号” / “这台机器已经登录过，不用再贴令牌”. Do not paste full CLI JSON.
