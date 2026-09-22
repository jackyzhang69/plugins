# Search mail

Use the typed agent entry, not free-form guessing:

```bash
printf '%s' '{"mailbox":"INBOX","filter":"subject","query":"...","account":"ACCOUNT_ID"}' \
  | anymail mail search-intent
```

## Coverage

- Default `coverage` is `one`: search exactly one account. If several accounts
  are ready and `account` is omitted, the envelope asks for account selection.
- For every ready account without claiming a false total, set
  `"coverage":"all_ready"` and omit `account`. The ready result includes
  `complete_for_ready_accounts` and per-account `status` values. If any ready
  account failed, say the search is incomplete.

## Envelope

When stdout is `jz.plugin.envelope.v1`, follow `status`, `needs`, and
`continue_args` exactly. Put protected continuation fields on stdin only.
Never put mailbox secrets in argv, environment, or chat.
