# Connect AnyImmi

## Shared platform token

- Canonical durable user credential: `~/.jackyzhang.app/token/user.json` (`jz_` only; `credential_kind=user`, `slot=user`).
- **One Portal user token for the whole platform.** FormBro, AnyChat, AnyPDF, AnyWeb, EasyBooks, AnyDoc, and AnyImmi share this file. If it already exists from any official plugin, do **not** ask the human to log in again and do **not** say AnyImmi needs a different Portal token.
- AnyImmi is **exchange mode**: the CLI calls `POST /v1/token/exchange` with `aud=anyimmi` and keeps a short-lived JWT in memory. Raw `jz_` is not a product bearer.
- Never print or log the raw token. Confirm with `anyimmi doctor --json` or a masked `anyimmi whoami --json`.
- Do not create a product-local durable token file. Runtime stays under `~/.jackyzhang.app/anyimmi/`.

## Already connected

Run:

```bash
anyimmi whoami --json
```

If authenticated, stop. Do not ask for another token.

## Token delivery (host agent — mandatory)

The host agent performs connect **for** the human. Never tell them to open a terminal and run login themselves.

Accept input in this order:

1. **File containing the token (preferred).** Read the path in the agent tool channel. Pipe stdin only:

```bash
printf %s "$(cat -- "$TOKEN_FILE")" | anyimmi login --token-stdin --json
```

2. **Plaintext token in chat (allowed, discouraged).** Warn once that a file path is better. Feed stdin. Do not echo the token back.

3. **No token yet.** Ask for a file path or a paste. Still do not ask them to run terminal commands.

Hard rules:

- `--token <value>` / argv secrets are **forbidden**.
- Never put a real `jz_` in skill text, logs, screenshots, or JSON the human sees.
- After a successful connect, other official plugins must not re-prompt when `user.json` is present.

After login, confirm with masked output:

```bash
anyimmi whoami --json
anyimmi doctor --json
```

`whoami` returns only masked token metadata, never raw tokens. `doctor` checks local backend resolution and credential safety. `logout` removes the saved local credential.

## Talk to the human

Plain language: “需要先登录一次免费账号” / “这台机器已经登录过，不用再贴令牌”. Do not paste full CLI JSON. Never give a terminal login command.
