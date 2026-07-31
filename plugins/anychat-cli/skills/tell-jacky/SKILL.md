---
name: tell-jacky
description: >-
  Submit a feature request, bug report, or knowledge tip for AnyChat ("Tell
  Jacky"). Always show a draft and get explicit user confirmation before sending.
when_to_use: |-
  "tell Jacky", "report a bug", "feature request", "save this tip", agent hit repeated AnyChat errors and user agrees.
---

# Tell Jacky (AnyChat)

## Cardinal rule

**Always show the exact type, title, description, context, and images** to the user
and wait for explicit confirmation before running create.

## Types

| Type | Flag |
|------|------|
| Feature | `--type feature-request` |
| Bug | `--type bug-report` |
| Tip | `--type knowledge-tip` |

## Command (after confirm)

```bash
"$ANYCHAT_BIN" feedback create \
  --type bug-report \
  --title "<≤200 chars>" \
  --description "<text>" \
  [--url "<url>"] \
  [--context-json '{"support_code":"E_…"}'] \
  [--image /path/screenshot.png] \
  --user-confirmed
```

`--user-confirmed` is **mandatory** and means the human approved the draft.

## PII / secrecy

- Do **not** include chat message bodies, friend names, wxids, tokens, keys, or attachment bytes.
- Bug reports: product support codes + redacted error text only.
- Scan screenshots for sensitive UI before attaching.

## After submit

Report returned `id` and status. Do not promise fix dates.
