# Tell Jacky (AnyDoc)

**plugin_id:** `anydoc`

## Multi-plugin sessions

- Mid AnyDoc flow → this skill.
- No product cue → **ask which product** before drafting.
- Draft always names **AnyDoc**.

Auth: CLI exchanges the owner Portal token for an `aud=anydoc` JWT before POST. Do not call HTTP yourself.

Resolve the binary: `$ANYDOC_BIN` if set, otherwise `anydoc` on PATH.

## Already connected

If `anydoc doctor --json` reports `credential.configured=false`, load [connect](connect.md) first. If `user.json` already exists, do not ask for another token.

## Talk to the human

Show the draft in plain language. After submit say it was sent (or saved only on this machine) and give the `id`. Do not paste full CLI JSON unless they ask.

**Cardinal rule:** every submission goes through `anydoc feedback create` with `--user-confirmed`. Never send without showing the exact type/title/description first.

## Data structure

| Field | Required | Notes |
|---|---|---|
| `type` | yes | `feature-request` · `bug-report` · `knowledge-tip` |
| `title` | yes | ≤200 chars |
| `description` | yes | concrete; no client document text |

CLI stamps `source=anydoc-cli` and client platform metadata.

## Router

| User says… | After draft confirm, run |
|---|---|
| bug / CLI error | `feedback create --type bug-report --title "…" --description "…" --user-confirmed --json` |
| feature | `feedback create --type feature-request --title "…" --description "…" --user-confirmed --json` |
| tip | `feedback create --type knowledge-tip --title "…" --description "…" --user-confirmed --json` |

## Mandatory draft confirmation

Before create, show:

1. exact `type`, `title`, `description`
2. that no client files, OCR text, or tokens will be attached

Wait for an explicit go-ahead.

## PII / secrecy

- **Do not** include client folder paths, document bytes, names, passport/UCI numbers, or tokens.
- Bug reports: product error `code` + redacted text only.

## After submit

- CLI prints JSON with `id` and `delivery` (`accountd` or `local_mirror` if offline / audience not deployed).
- Report the `id`. Do not promise fix dates.
- `feedback list` / `feedback status --id …` inspect own items.
- `feedback inbox` shows unread replies; mark each read with `feedback read --update-id <id>` after showing it.

## Advisory inbox check

On the first authenticated AnyDoc action in a session, best-effort:

```bash
"$ANYDOC_BIN" feedback inbox --json
```

If unread replies exist, show them, then `feedback read --update-id …`. If inbox fails, continue. Never inject inbox data into another command's stdout.

## Failure handling

- **not_logged_in**: `references/connect.md`.
- **unknown_audience** / **accountd_unreachable** / `delivery=local_mirror`: saved on this machine; not yet in Jacky's inbox — say so honestly.
- Other errors: surface the CLI `code` and `error`.
