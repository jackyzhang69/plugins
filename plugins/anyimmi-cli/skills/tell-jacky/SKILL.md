---
name: tell-jacky
description: Submit a feature request, bug report, or knowledge tip for AnyImmi on the user's behalf ("Tell Jacky"). Collects title/description, always shows a draft for user confirmation before sending, and mirrors submissions locally.
when_to_use: |-
  Trigger phrases:
    - "tell Jacky about this"
    - "report this bug in AnyImmi"
    - "this would be a good AnyImmi feature request"
    - "note this as a tip for AnyImmi"
---

# Tell Jacky (AnyImmi)

Submits feedback directly to Jacky's unified product feedback inbox via `anyimmi feedback`.

**Cardinal rule:** Every submission goes through `anyimmi feedback`. Never send anything without showing the user the exact draft and getting explicit confirmation.

## Command

```bash
anyimmi feedback --type <feature-request|bug-report|knowledge-tip> --title "<short summary>" --description "<details>" --user-confirmed
```

## Mandatory Draft Confirmation

Before executing `anyimmi feedback`, always show the user:
1. Exact `type`: `feature-request`, `bug-report`, or `knowledge-tip`
2. `title`: Concise summary (≤ 200 characters)
3. `description`: Verbatim details or steps to reproduce

Wait for the user's explicit go-ahead before running the command. All submissions are automatically mirrored locally at `~/.jackyzhang.app/anyimmi/feedback.json`.
