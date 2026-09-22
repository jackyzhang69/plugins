# Tell Jacky (AnyPDF)

**plugin_id:** `anypdf`

Inbox: Portal / accountd `product_feedback`.

This is one of two valid public ways to ask for a missing form. Use it when the
user wants Jacky to support a form and has no issuing-authority official blank
PDF, or for a feature / bug / tip. When they already have that blank PDF, use
[intake](intake.md) (direct form request) instead — never put PDF bytes in a
Tell Jacky report.

## Talk to the human

Show the draft in plain language; after submit say “已发给 Jacky，编号 …” without pasting full CLI JSON unless they ask.

Run [connect](connect.md) first so the installed package repairs the canonical
client slot. Set `ANYPDF` to that slot as instructed there; every agent command
below uses `$ANYPDF`, never whichever `anypdf` is first on PATH.

**Cardinal rule:** every submission goes through `$ANYPDF feedback submit` after the user confirms the draft. Never call Portal HTTP directly.

## Multi-plugin sessions

- Mid AnyPDF flow → this skill.
- No product cue → **ask which product** before drafting.
- Draft always names **AnyPDF**.

```bash
$ANYPDF feedback submit --report /absolute/report.json --idempotency-key <stable-key>
$ANYPDF feedback status --report-id <id>
```

Never put tokens, PDF bytes, filled field values, or customer identity in the report.

## New-form ask (text only)

When the user asks to add or support a form without providing an official blank
PDF, draft a short Tell Jacky note that names the form (title, code, or
issuing authority) and that no blank was uploaded. Confirm the draft, then
submit. Do not promise a date or that intake ran. If they later provide a blank
PDF, switch to [intake](intake.md).

## Recovery before escalation

Tell Jacky is not the first recovery step. First inspect typed recovery and
finish the bounded supported action in [fill](fill.md): one status re-read,
one same-key retry where authorized, one readiness retry, or reconnect through
[connect](connect.md) after transport authentication is exhausted. Only a
reportable or reproducibly exhausted problem is eligible here.

Prepare a plain, redacted draft that describes the outcome, not tool internals.
Exclude submitted values, PDF files, credentials, local paths, raw command
output, and stack traces. Show the draft and wait for explicit human consent;
never send feedback automatically.
