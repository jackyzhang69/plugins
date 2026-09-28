# Draft and confirm-send

Local drafts never create cloud drafts.

## Draft JSON input

`mail draft create`, `reply`, `forward`, and `update` read this bounded JSON
object from stdin. Unknown fields are rejected.

Required fields:

- `from`: string
- `to`: array of strings
- `body`: string

Optional fields:

- `cc`, `bcc`, `references`: arrays of strings
- `subject`, `in_reply_to`, `signature`: string or `null`
- `attachments`: array of attachment objects

Each attachment requires string fields `id`, `filename`, and `data_base64`;
`data_base64` contains the attachment bytes encoded as Base64. Optional
`content_type` is a string and defaults to `application/octet-stream`.

Minimal local-draft example using reserved test addresses:

```bash
anymail mail draft create --account ACCOUNT_ID <<'JSON'
{
  "from": "sender@example.test",
  "to": ["recipient@example.test"],
  "body": "Draft text"
}
JSON
```

## Confirmed send flow

1. Create from bounded JSON on stdin:

```bash
cat draft.json | anymail mail draft create --account ACCOUNT_ID
```

2. Optional read/update/delete with exact revision checks.
3. Freeze and preview:

```bash
anymail mail draft prepare --account ACCOUNT_ID --draft-id DRAFT_ID
```

4. Show the human the exact preview fields (from, to, subject, body,
   attachments). Keep `challenge_token` between tools.
5. After explicit confirmation, consume the token once on stdin:

```bash
printf '%s' "$CHALLENGE_TOKEN" | anymail mail draft confirm-send --operation-id OPERATION_ID
```

6. Report the durable `result` (`accepted` / `failed` / `unknown`). Do not
   invent a provider message id for Graph `accepted`. Do not auto-retry
   `unknown`. Use `mail draft status` / `dispatch-confirmed` only for an
   already confirmed operation that still needs dispatch.

The account must already be send-ready through the explicit upgrade command.
