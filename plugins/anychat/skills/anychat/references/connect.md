## Shared platform token (host agent — mandatory)

- Canonical durable user credential: `~/.jackyzhang.app/token/user.json` (`jz_` only; `credential_kind=user`, `slot=user`).
- **One Portal user token for the whole platform.** FormBro, AnyChat, AnyPDF, AnyWeb, EasyBooks, AnyDoc, and AnyImmi all use this same file. If it already exists from any official plugin, do **not** ask the human to log in again and do **not** say this product needs a different Portal token.
- **Consumption differs by product; the durable token does not.**
  - **Exchange mode** (`anychat`, `anypdf`, `anyweb`, EasyBooks/`eb`): CLI calls `POST /v1/token/exchange` with `aud=<product>` and uses a short-lived memory-only JWT on product routes. Raw `jz_` is not a product bearer.
  - **Introspect mode** (`formbro`): CLI/API sends raw `jz_`; FormBro backend calls accountd `POST /v1/api-tokens/introspect`. `aud=formbro` exchange is invalid (`unknown_audience`).
- Retired local prefixes (`fb_`, `ap_live_`, `eb_live_`, …) are not Portal credentials — never ask the human to paste them into a Portal plugin.
- On load, if `user.json` is missing but a legacy on-disk `jz_` exists (`token/jz.json` or migratable product-local fields), **migrate immediately** via this product CLI `whoami`/`doctor`/login path. User must feel no re-paste.
- Never print or log the raw token. Prefer masked doctor/whoami output.
- Do not create product-local durable token files. Plugin runtime stays under `~/.jackyzhang.app/<plugin_id>/` only.


## Token delivery to the host agent (connect) — LOCKED 2026-08-24

Same contract as every official plugin. The host agent performs connect **for** the human. **Never** tell the human to open a terminal and run login commands themselves. This block is the whole connect path; later steps must not replace it with a human-typed terminal prompt.

Accept input in this order:

1. **File containing the token (preferred).** If the human provides a filesystem path (e.g. `~/Desktop/jacky-token.txt`) or an attached/readable file whose contents are a single `jz_…` value (optional surrounding whitespace/newline only):
   - Read the file in the agent tool channel.
   - Pipe the token to the product CLI via stdin only:

```bash
printf %s "$(cat -- "$TOKEN_FILE")" | "$ANYCHAT_BIN" login --token-stdin --accept-personal-use
```

   - Do **not** put the token on argv, in chat echo, in logs, or in screenshots.
   - Confirm success with masked doctor/whoami only.

2. **Plaintext token in chat (allowed, discouraged).** If the human pastes a raw `jz_…` into the conversation:
   - **Warn once** in plain language: prefer a local file path next time so the secret is not retained in chat history; do **not** refuse.
   - Proceed immediately: feed that value to `login --token-stdin` via a non-echoing agent-side pipe (temp file mode 0600 deleted after use is OK; never `echo TOKEN |` in a way that lands in shell history if avoidable).
   - Do not repeat the raw token back in the reply.

3. **No token yet.** Ask the human to send either a **file path** (best) or paste the token. Still do not ask them to run terminal commands.

Hard rules:

- `--token <value>` / argv secrets remain **forbidden** for the CLI.
- Agent may read a user-supplied path and stdin-feed the CLI; that is the supported file path.
- After any successful connect, other plugins must not re-prompt when `user.json` is present.


# Connect AnyChat

## Advisory marketplace check

On first AnyChat use in each host-agent session, run `"$ANYCHAT_BIN" doctor --check-upgrade --json` before the requested action. If `upgrade.status` is `update_available`, briefly recommend updating, then continue normally. Never auto-update, never block the user, and continue silently when the check is unavailable. Run this once per session load, not before every command. If the host marketplace GitHub clone times out, download `https://github.com/jackyzhang69/plugins/archive/refs/heads/main.zip` yourself. Never `git clone` that repository as the update path.

## Talk to the human

Keep chat plain: “需要先登录一次免费账号” / “登录成功，下一步准备本机档案”. Do not paste `whoami --json` or full CLI help into the reply (use them only as tools). Follow the product router § **Talk to the human**.

## Already connected

If `anychat whoami` succeeds and entitlement is valid, skip token capture.

## Portal online check

`login` tries Portal HTTPS entitlement first (`POST …/v1/products/anychat/entitlement`).  
If Portal is unreachable, AnyChat uses **offline-grace** (~7d local cache) and still fails closed after expiry.  
Force offline-only: `ANYCHAT_PORTAL_OFFLINE=1`.  
Optional hard revalidation on each query: `ANYCHAT_FORCE_REVALIDATE=1`.
Still run `anychat doctor` when setup may be incomplete.

## First-time path

1. Confirm personal-use terms with the user (own data, own machine only).
2. Resolve the `anychat` binary (see the product router §B).
3. Follow **Token delivery** above. Product flag: `--accept-personal-use`. Never print a TTY login command. Never use `--token`.
4. Mask any token in logs as `****` / prefix only.
5. Then: `anychat doctor` → if `setup_needed`, read [setup](setup.md).
6. Stay on the product router for session routing.

## Token rules

- Never echo the full token.
- Never put the secret on process argv (`--token` is forbidden; `--token-stdin` only).
- Store only via CLI (`~/.jackyzhang.app/token/user.json` and `~/.jackyzhang.app/anychat/` runtime).
- Never put a real token value in skill examples.
