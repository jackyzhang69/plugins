---
name: tell-jacky
description: >-
  Submit a feature request, bug report, or knowledge tip for AnyChat ("Tell
  Jacky") to the Portal feedback backend via the bundled CLI — same ritual as
  FormBro tell-jacky and AnyPDF feedback. Always show a draft and get explicit
  user confirmation before sending. Requires connect-anychat (login) first.
when_to_use: |-
  Trigger phrases:
    - "tell Jacky about this"
    - "report this bug / file a bug report"
    - "feature request for AnyChat"
    - "note this as a tip"
    - agent hit repeated AnyChat CLI/setup errors (E_SETUP_*, E_PORTAL_*) and the user agrees to send
---

# Tell Jacky (AnyChat)

Submits feedback to the **Portal / accountd** product-feedback store (same owner
token as login), via the bundled `anychat` CLI. Resolve the binary once via
`anychat-capabilities` §B.

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
| `context-json` | no | e.g. `{"support_code":"E_SETUP_PENDING","os":"windows"}` |
| `image` | no | local screenshot path(s); confirm each |

CLI always stamps `context.source = anychat-cli` and client platform metadata.

## Router

| User says… | After draft confirm, run |
|---|---|
| bug / CLI error | `feedback create --type bug-report --title "…" --description "…" [--context-json '…'] --user-confirmed` |
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
- `feedback status --id …` / `feedback list` inspect own items.

## Failure handling

- **401/403**: re-login (`connect-anychat`); token missing/expired.
- **`E_FEEDBACK_OFFLINE`**: saved locally; will not reach Jacky until Portal is reachable — tell the user honestly.
- Other errors: surface CLI text verbatim.

## Proactive offer (opt-in only)

On repeated `E_SETUP_*` / setup failures after user frustration: ask once  
“要不要告诉 Jacky？” — never auto-send.
