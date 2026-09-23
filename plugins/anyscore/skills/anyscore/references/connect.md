# Connect AnyScore

## Shared platform token

- Canonical durable user credential: `~/.jackyzhang.app/token/user.json` (`jz_` only; `credential_kind=user`, `slot=user`).
- **One Portal user token for the whole platform.** FormBro, AnyChat, AnyPDF, AnyWeb, EasyBooks, AnyDoc, AnyImmi, and AnyScore share this file. If it already exists from any official plugin, do **not** ask the human to log in again and do **not** say AnyScore needs a different Portal token.
- AnyScore is **exchange mode**: the CLI calls `POST /v1/token/exchange` with `aud=anyscore` and keeps a short-lived JWT in memory. Raw `jz_` is not a product bearer.
- Never print or log the raw token. Confirm with `anyscore doctor --json` or a masked `anyscore whoami --json`.
- Do not create a product-local durable token file. Runtime stays under `~/.jackyzhang.app/anyscore/`.

## Already connected

```bash
anyscore whoami --json
```

If authenticated, stop. Do not ask for another token.

## Token delivery (host agent — mandatory)

The host agent performs connect **for** the human. Never tell them to open a terminal and run login themselves.

1. **File containing the token (preferred).**

```bash
printf %s "$(cat -- "$TOKEN_FILE")" | anyscore login --token-stdin --json
```

2. **Plaintext token in chat (allowed, discouraged).** Warn once that a file path is better. Feed stdin. Do not echo the token back.

3. **No token yet.** Ask for a file path or a paste. Still do not ask them to run terminal commands.

Hard rules:

- `--token <value>` / argv secrets are **forbidden**.
- Never put a real `jz_` in skill text, logs, screenshots, or JSON the human sees.
- After a successful connect, other official plugins must not re-prompt when `user.json` is present.

```bash
anyscore whoami --json
anyscore doctor --json
```

## Talk to the human

Plain language: “需要先登录一次免费账号” / “这台机器已经登录过，不用再贴令牌”. Do not paste full CLI JSON.
