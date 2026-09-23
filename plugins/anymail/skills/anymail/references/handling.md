# Remember pin / quiet handling rules

Server-side confirmed rules only (`anymail-handling-v0`). They change how
thread and history summaries are annotated (`handling.effect` = `pin` or
`quiet`). They never send mail and never create reply drafts.

First ship kinds:

| kind | effect | match |
|---|---|---|
| `vip_sender` | pin | one email address |
| `mute_sender` | quiet | one email address |
| `mute_domain` | quiet | hostname only |

## List

```bash
anymail mail handling-list
```

## Remember (two-step confirm)

1. Preview the exact draft text (stdin JSON, no `--user-confirmed`):

```bash
printf '%s' '{"kind":"mute_sender","match":"ads@example.com"}' \
  | anymail mail handling-remember
```

2. Show `exact_text` to the human. Only after they approve that exact wording,
   re-run with `--user-confirmed`:

```bash
printf '%s' '{"kind":"mute_sender","match":"ads@example.com"}' \
  | anymail mail handling-remember --user-confirmed
```

Never invent a different confirmation phrase. Never skip the preview step.

## Forget

```bash
anymail mail handling-forget --rule-id RULE_UUID --confirm forget
```

## Autonomy (locked)

- Organize summaries only (pin / quiet annotations).
- Create drafts only when the human asks.
- Never send without the ordinary exact-preview confirm path.
