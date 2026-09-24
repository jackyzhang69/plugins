# Remember short AnyMail instructions

Server-side confirmed free-text memories only (`remember-me-v0` AnyMail
binding). They guide how future mail work is handled (tone, when to offer a
draft). They never store mailbox content, never pin/mute by themselves, and
never authorize send.

First ship scope: `global` only.

## List

```bash
anymail mail memory-list
```

## Remember (two-step confirm)

1. Preview the exact draft text (stdin JSON, no `--user-confirmed`):

```bash
printf '%s' '{"text":"Prefer short draft replies when I ask."}' \
  | anymail mail memory-remember
```

2. Show `exact_text` to the human. Only after they approve that exact wording,
   re-run with `--user-confirmed`:

```bash
printf '%s' '{"text":"Prefer short draft replies when I ask."}' \
  | anymail mail memory-remember --user-confirmed
```

Never invent a different confirmation phrase. Never skip the preview step.
Never write mailbox content or harvested identities into memory text.

## Forget

```bash
anymail mail memory-forget --memory-id MEMORY_UUID --confirm forget
```

## Autonomy (locked)

- Text may guide presentation and whether the agent offers to draft.
- Create drafts only when the human asks.
- Never send without the ordinary exact-preview confirm path.
- Pin/mute/archive still use [handling](handling.md), not free text.
