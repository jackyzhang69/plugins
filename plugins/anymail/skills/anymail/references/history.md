# Check for new mail changes

Use the typed agent entry. AnyMail keeps the incremental cursor locally; do
not invent or ask the human for a history cursor.

```bash
printf '%s' '{"account":"ACCOUNT_ID"}' | anymail mail history-intent
```

First successful call seeds the cursor and returns an empty change set.
Later calls return only the changes since that seed. To re-seed deliberately:

```bash
printf '%s' '{"account":"ACCOUNT_ID","reset_cursor":true}' | anymail mail history-intent
```

## Envelope

When stdout is `jz.plugin.envelope.v1`, follow `status`, `needs`, and
`continue_args` exactly. Put protected continuation fields on stdin only.
Never put mailbox secrets in argv, environment, or chat.

## Notes

- Incremental change checks are Gmail-ready operations.
- Ready payloads may include `handling.effect` (`pin` / `quiet`) from confirmed
  server rules; treat quiet items as lower priority unless the human asks.
- Low-level `mail profile-get` / `mail history-list` exist for tooling; host
  agents should stay on `mail history-intent`.
