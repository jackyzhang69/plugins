# Tell Jacky (AnyPDF)

**plugin_id:** `anypdf`

Inbox: Portal / accountd `product_feedback`.

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
