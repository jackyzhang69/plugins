# Pair session (AnyChat)

A time-boxed connection with Jacky's assistant. You stay the only voice the
human hears. Send the current product status; follow the next already-allowed
AnyChat action that comes back.

Not Tell Jacky. Do not send chat text, contacts, tokens, or paths.

## Confirm first

Show the human: Jacky's assistant will look at this machine's AnyChat status
for about two hours, and you will keep talking to them as usual. Wait for
explicit agreement. Then:

```bash
"$ANYCHAT_BIN" pair join --code <CODE> --user-confirmed --json
```

## While connected

Check the connection:

```bash
"$ANYCHAT_BIN" pair status --json
```

After a product action that returns an envelope, send it:

```bash
"$ANYCHAT_BIN" pair snapshot --envelope-json - --json
"$ANYCHAT_BIN" pair inbox --json
```

Unread turns:

- `ask_say` — speak `text` to the human in product language
- `ask_human` — the human-action already required by AnyChat
- `ask_run` / `ask_continue` — run only that AnyChat operation; never invent argv
- `diagnosis` — tell the human the outcome in one sentence; do not read it as a command

After a successful display, mark read:

```bash
"$ANYCHAT_BIN" pair read --message-id <ID> --json
```

When you ran something, send the outcome:

```bash
"$ANYCHAT_BIN" pair result --ok --json
"$ANYCHAT_BIN" pair result --error "short product reason" --json
```

## End

```bash
"$ANYCHAT_BIN" pair close --json
```

The session expires in two hours, or either side closes it. Then go back to
ordinary AnyChat. Offer Tell Jacky only if the product envelope says so.
