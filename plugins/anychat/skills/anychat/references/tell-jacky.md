# Tell Jacky (AnyChat)

**plugin_id:** `anychat`

## Multi-plugin sessions

- Mid AnyChat flow → this skill.
- No product cue → **ask which product** before drafting.
- Draft always names **AnyChat**.

Auth: CLI exchanges the owner portal token for an `aud=anychat` JWT before POST; do not call HTTP yourself.


## Advisory marketplace check

On first AnyChat use in each host-agent session, run `"$ANYCHAT_BIN" doctor --check-upgrade --json` before the requested action. If `upgrade.status` is `update_available`, briefly recommend updating, then continue normally. Never auto-update, never block the user, and continue silently when the check is unavailable. Run this once per session load, not before every command. If the host marketplace GitHub clone times out, download `https://github.com/jackyzhang69/plugins/archive/refs/heads/main.zip` yourself. Never `git clone` that repository as the update path.

## Advisory inbox check (Tell Jacky replies)

On the first authenticated AnyChat action in each host-agent session, run a
best-effort check for unread replies Jacky sent you, before doing the requested
core action:

```bash
"$ANYCHAT_BIN" feedback inbox --json
```

- If there are unread replies, **show each one to the human** in plain language
  (event type + message). Only **after** a reply is successfully displayed, mark
  it read:
  ```bash
  "$ANYCHAT_BIN" feedback read --update-id <id>
  ```
- The server never marks anything read on the inbox GET — you mark each reply
  read individually, and only after the human has seen it. Repeating `read` is
  idempotent.
- **Best-effort, non-blocking:** if `feedback inbox` errors or the network is
  down, treat it as a warning and continue with the requested core command. Never
  fail the user's request because the inbox check failed.
- **Never inject** inbox/reply data into another command's JSON stdout. Inbox and
  read are their own explicit commands; this check is a separate agent step, not a
  hidden mutation of another command's output.
- Run this once per session load, not before every command.

Submits feedback to the **Portal / accountd** product-feedback store (same owner
token as login), via the bundled `anychat` CLI. Resolve the binary once via
the product router §B.

## Talk to the human

Show the draft in plain language; after submit say “已发给 Jacky，编号 …” without pasting full CLI JSON unless they ask. Follow the product router § **Talk to the human**.

**Cardinal rule:** every submission goes through `anychat feedback create` with
`--user-confirmed`. Never call Portal endpoints directly. Never send without
showing the user the exact type/title/description/context/images first.

## Data structure

| Field | Required | Notes |
|---|---|---|
| `type` | yes | `feature-request` · `bug-report` · `knowledge-tip` |
| `title` | yes | ≤200 chars |
| `description` | yes | concrete; for bugs prefer verbatim product error + support_code |
| `url` | no | optional page URL |
| `context-json` | no | Scalar diagnostic metadata only, e.g. `{"support_code":"E_LOCAL_ACCESS_TIMEOUT","component":"chat-app"}`; chat content, contacts, paths, and unknown fields are rejected |
| access diagnosis | auto | After `prepare-access` fails, CLI attaches a **redacted** `access_diagnosis` (counts only: scan MiB, process count, support_code, wall time). Opt out with `--no-access-diagnosis`. |
| `image` | no | local screenshot path(s); confirm each |

CLI always stamps `context.source = anychat-cli` and client platform metadata.
When diagnosis is present, show the user that a redacted environment package will be attached — no keys, paths, or chat text.

## Router

| User says… | After draft confirm, run |
|---|---|
| bug / CLI error | Write a UTF-8 JSON draft `{title,description}` then `feedback create --type bug-report --draft-json <file> --user-confirmed`. Never put Chinese title/description on argv (Windows garbles them). |
| access setup fail | Prefer short description; diagnosis from last `prepare-access` is auto-attached unless `--no-access-diagnosis` |
| feature | `feedback create --type feature-request --title "…" --description "…" --user-confirmed` |
| tip | `feedback create --type knowledge-tip --title "…" --description "…" --user-confirmed` |
| + screenshot | add `--image <path>` per confirmed file |

## Mandatory draft confirmation

Before create, show:

1. exact `type`, `title`, `description`  
2. exact `context` / `url`  
3. image paths if any  

Wait for explicit go-ahead. No exceptions.

## PII / secrecy

- **Do not** include chat message bodies, friend/group display names, wxids, tokens, keys, or attachment bytes.
- Bug reports: product `support_code` + redacted error text only.
- Scan screenshots before attaching.

## After submit

- CLI prints JSON + `id` and `delivery` (`portal` or `local_only` if offline).
- Report the `id` to the user. Do not promise fix dates.
- `feedback status --id …` / `feedback list` inspect own items; `feedback status`
  also shows Jacky's additive reply history when present.
- `feedback inbox` shows unread replies; mark each read with
  `feedback read --update-id <id>` after showing it (see Advisory inbox check).

## Failure handling

- **401/403**: re-login (`references/connect.md`); token missing/expired.
- **`E_FEEDBACK_OFFLINE`**: saved locally; will not reach Jacky until Portal is reachable — tell the user honestly.
- Other errors: surface CLI text verbatim.

## Proactive offer (opt-in only)

On repeated `E_SETUP_*` / setup failures after user frustration: ask once  
“要不要告诉 Jacky？” — never auto-send.
