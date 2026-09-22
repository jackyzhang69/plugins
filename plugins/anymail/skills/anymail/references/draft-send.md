# Draft and confirm-send

Local drafts never create cloud drafts.

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
