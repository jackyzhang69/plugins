---
name: anymail
description: >-
  READ THIS FIRST for AnyMail. Open Gmail or Microsoft mailboxes through official
  OAuth, search/read/attachments, local drafts, exact preview, one-time confirm-send.
  Thread/history intents, confirmed pin/quiet handling rules, organize-only autonomy.
  Discover the live CLI surface; never invent providers, scopes, or secrets.
  One discovery file; playbooks in references/.
when_to_use: |-
  Load on plugin start. "open my Gmail/Outlook", "open Gmail", "open Outlook",
  "search mail", "read an email", "read a conversation", "check for new mail",
  "download attachment", "draft and send", "pin this sender", "mute this sender",
  "what can AnyMail do", "Tell Jacky".
---

# AnyMail — what this plugin can do

Load this on plugin start, and again when the user asks what AnyMail can do or
how to read or send mail.

## Talk to the human

Speak in everyday words. Say what you can do and what happens next. Do not
lead with binary paths, `--help`, or raw JSON. Keep CLI and JSON between
tools. Never show credentials, OAuth client ids, tokens, challenge tokens, or
refresh material. Never ask the human to create an OAuth app, paste a token,
or type a mailbox password into chat. Run the intended ordinary command first;
when stdout is `jz.plugin.envelope.v1`, follow its exact `status` and
`continue_args` until the product reports `ready`, then resume the sealed
request.

## Autonomy (locked)

- Organize only: summaries may pin or quiet senders from confirmed server rules.
- Create drafts only when the human asks.
- Never send without the ordinary exact-preview confirm path.
- Never invent send, auto-reply, or unconfirmed remember/forget.

## When the user asks "what can you do?"

Use this as a runtime answer harness. The answer is valid only after live
discovery.

1. Run:

```bash
$ANYMAIL doctor
$ANYMAIL commands
$ANYMAIL account list --json
```

2. Treat returned values as current facts. Current product paths include Gmail
   and Microsoft Graph OAuth, plus discovered standard IMAP/SMTP password or
   app-password accounts. Do not invent providers that `commands` / discovery
   do not return.

3. Translate into everyday product language. Keep CLI names, JSON schemas,
   paths, and implementation details between tools.

## Agent router — intents

| User intent | Host does | Human may be asked |
|---|---|---|
| "what can AnyMail do" | Live `doctor` + `commands` + `account list` | Connect/open once if no mailbox is ready |
| Open Gmail / Outlook / standard mailbox | [open-account](references/open-account.md); app-password providers use [app-password](references/app-password.md) | Official browser consent, or local governed app-password storage |
| Search / find mail | [search](references/search.md) via `mail search-intent` | Which account, only when coverage is `one` and several are ready |
| Read a conversation thread | [thread](references/thread.md) via `mail thread-intent` | Which Gmail account, only when several are ready |
| Check for new mail changes | [history](references/history.md) via `mail history-intent` | Which Gmail account, only when several are ready |
| Pin or quiet a sender/domain | [handling](references/handling.md) via `mail handling-*` | Confirm the exact draft text before remember/forget |
| Read message / attachment | `mail fetch` / `mail attachment` / `mail export` with exact ids from search | Nothing technical |
| Draft and send | [draft-send](references/draft-send.md); reply/forward via `mail draft reply|forward` | Confirm the exact preview before send |
| Tell Jacky | [tell-jacky](references/tell-jacky.md) | Confirm the exact draft; only when the feedback gate is live |

Triggers: [triggers](references/triggers.md).

Playbooks: [get-started](references/get-started.md),
[open-account](references/open-account.md), [search](references/search.md),
[thread](references/thread.md), [history](references/history.md),
[handling](references/handling.md), [draft-send](references/draft-send.md),
[tell-jacky](references/tell-jacky.md).

## Live CLI discovery (fail-closed)

Ask the live CLI, never from memory of an older release.

```bash
"$ANYMAIL" commands
```

Command path reference: [references/command-surface.md](references/command-surface.md).

## Shared safety core

Gmail and Microsoft share the same confirmation model: local draft → exact
preview → one-time challenge on stdin → send status. Do not invent a second
confirmation path. Accepted Graph results may lack a provider message id;
report the durable `result` honestly and never auto-retry unknown outcomes.
