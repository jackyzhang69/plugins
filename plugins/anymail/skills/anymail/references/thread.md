# Read a conversation

Use the typed agent entry, not free-form leaf commands:

```bash
printf '%s' '{"action":"list","query":"from:example@domain","account":"ACCOUNT_ID"}' \
  | anymail mail thread-intent
```

```bash
printf '%s' '{"action":"get","thread_id":"THREAD_ID","format":"metadata","account":"ACCOUNT_ID"}' \
  | anymail mail thread-intent
```

## Envelope

When stdout is `jz.plugin.envelope.v1`, follow `status`, `needs`, and
`continue_args` exactly. Put protected continuation fields on stdin only.
Never put mailbox secrets in argv, environment, or chat.

## Notes

- Conversation listing and reading are Gmail-ready operations.
- Prefer `format":"metadata"` unless the human needs full message bodies.
- Ready payloads may include `handling.effect` (`pin` / `quiet`) from confirmed
  server rules; surface pin first and de-emphasize quiet unless asked.
- Low-level `mail thread-search` / `mail thread-get` exist for tooling; host
  agents should stay on `mail thread-intent`.
