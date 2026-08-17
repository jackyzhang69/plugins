---
name: tell-jacky
description: >-
  Submit a feature request, bug report, or knowledge tip for AnyPDF ("Tell
  Jacky") to the Portal product-feedback backend via the bundled CLI. Always show a
  draft and get explicit user confirmation before sending. Requires
  connect-anypdf (login) first. plugin_id is bound to anypdf.
---

# Tell Jacky (AnyPDF)

**plugin_id:** `anypdf`

Inbox: Portal / accountd `product_feedback`.

## Talk to the human

Show the draft in plain language; after submit say “已发给 Jacky，编号 …” without pasting full CLI JSON unless they ask.

**Cardinal rule:** every submission goes through `anypdf feedback submit` after the user confirms the draft. Never call Portal HTTP directly.

## Multi-plugin sessions

- Mid AnyPDF flow → this skill.
- No product cue → **ask which product** before drafting.
- Draft always names **AnyPDF**.

```bash
anypdf feedback submit --report /absolute/report.json --idempotency-key <stable-key>
anypdf feedback status --report-id <id>
```

Never put tokens, PDF bytes, filled field values, or customer identity in the report.
