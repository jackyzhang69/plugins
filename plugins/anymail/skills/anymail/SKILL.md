---
name: anymail
description: >-
  READ THIS FIRST for AnyMail. Open Gmail or Microsoft mailboxes through official
  OAuth, search/read/attachments, local drafts, exact preview, one-time confirm-send.
  Thread/history intents, confirmed pin/quiet handling rules, private remember-me
  short instructions, organize-only autonomy.
  Discover the live CLI surface; never invent providers, scopes, or secrets.
  One discovery file; playbooks in references/.
when_to_use: |-
  Load on plugin start. "open my Gmail/Outlook", "open Gmail", "open Outlook",
  "search mail", "read an email", "read a conversation", "check for new mail",
  "download attachment", "draft and send", "pin this sender", "mute this sender",
  "reauthorize an existing mailbox", "remember this preference",
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
- Short remember-me text may guide tone and when to offer a draft.
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

2. Treat returned values as current facts. Current product paths are Gmail and
   Microsoft Graph OAuth accounts; standard IMAP/SMTP mailboxes cannot be
   opened in the published plugin, though an already existing one can still be
   used. Do not invent providers that `commands` / discovery do not return.

3. Translate into everyday product language. Keep CLI names, JSON schemas,
   paths, and implementation details between tools.

## Agent router — intents

| User intent | Host does | Human may be asked |
|---|---|---|
| "what can AnyMail do" | Live `doctor` + `commands` + `account list` | Connect/open once if no mailbox is ready |
| Open Gmail / Outlook / standard mailbox | [open-account](references/open-account.md); app-password providers use [app-password](references/app-password.md) | Official browser consent for Gmail and Outlook. A standard IMAP/SMTP mailbox cannot be opened in the published plugin; tell the user and offer Gmail or Outlook |
| Reauthorize an existing mailbox | [open-account](references/open-account.md) | Official OAuth consent, or replacement credentials through a secure local stdin path |
| Search / find mail | [search](references/search.md) via `mail search-intent` | Which account, only when coverage is `one` and several are ready |
| Read a conversation thread | [thread](references/thread.md) via `mail thread-intent` | Which Gmail account, only when several are ready |
| Check for new mail changes | [history](references/history.md) via `mail history-intent` | Which Gmail account, only when several are ready |
| Pin or quiet a sender/domain | [handling](references/handling.md) via `mail handling-*` | Confirm the exact draft text before remember/forget |
| Remember a short mail preference | [remember-me](references/remember-me.md) via `mail memory-*` | Confirm the exact draft text before remember/forget |
| Read message / attachment | `mail fetch` / `mail attachment` / `mail export` with exact ids from search | Nothing technical |
| Draft and send | [draft-send](references/draft-send.md); reply/forward via `mail draft reply|forward` | Confirm the exact preview before send |
| Tell Jacky | [tell-jacky](references/tell-jacky.md) | Confirm the exact draft; only when the feedback gate is live |

Triggers: [triggers](references/triggers.md).

Playbooks: [get-started](references/get-started.md),
[open-account](references/open-account.md), [search](references/search.md),
[thread](references/thread.md), [history](references/history.md),
[handling](references/handling.md), [remember-me](references/remember-me.md),
[draft-send](references/draft-send.md),
[tell-jacky](references/tell-jacky.md),
[whats-new](references/whats-new.md).

## Live CLI discovery (fail-closed)

Ask the live CLI, never from memory of an older release.

```bash
"$ANYMAIL" commands
```

The list below names every command of this release with its key flags, and it is complete. The live `commands` output adds every argument's meaning and confirms that the installed version matches this list.

## Every command, one line each

Each line is the command path with its key flags. Mail commands take `--account ACCOUNT_ID` from `account list`. Every command prints JSON. The optional `--json` flag is accepted only by `version`, `commands`, `doctor`, `login`, `logout`, `whoami`, every `feedback` command and every `account` command; `mail` commands take no `--json`.

### Install, identity and discovery

- `version` — print the installed AnyMail version.
- `commands` — print the live command surface and capability list.
- `doctor` — report install, runtime and running-client state; `doctor --repair-install` installs the verified package containing the running binary (see "Install status and repair" in [open-account](references/open-account.md)).
- `login --token-stdin --force` — save the shared Portal credential, read from stdin; `--force` replaces an existing one.
- `logout` — clear the saved Portal credential.
- `whoami` — show the masked Portal identity for this product.

### Tell Jacky

- `feedback create` — preview the exact feedback draft; `--type` is feature-request, bug-report or knowledge-tip. Pass `--user-confirmed` only after the human approves the preview. Optional: `--type`, `--title`, `--description`, `--user-confirmed`.
- `feedback list` — list feedback already sent.
- `feedback status` — show one feedback report. Optional: `--id`.
- `feedback inbox` — list unread updates from Jacky.
- `feedback read` — mark one update as read. Optional: `--update-id`.

### Accounts

- `account list` — list local mailbox accounts without contacting providers.
- `account status --account` — show one local account.
- `account discover --email` — read-only check of what a standard IMAP/SMTP mailbox would use; it changes nothing and needs no credentials.
- `account open --email` — NOT available in the published plugin for a standard IMAP/SMTP mailbox: it never accepts a password (not on argv, stdin, environment, a prompt or an OS keychain) and a shipped build always stops with an error (see [app-password](references/app-password.md)). Tell the user and offer Gmail (`account open-google`) or Microsoft (`account open-microsoft`) instead. Flags, for reference: `--credential-kind` is `password` or `app-password` and selects what discovery looks for; `--account-id` is the local account label (default: the email); `--read-login` and `--send-login` are the IMAP and SMTP login names, not secrets, that override the username discovery found (default: that username, else the email), and at least one must equal the email or the command is rejected as invalid input; `--from` is the sender address for outgoing mail (default: the email) and must be a valid address. Optional: `--account-id`, `--read-login`, `--send-login`, `--from`, `--credential-kind`.
- `account open-google` — open Gmail through browser OAuth. Optional: `--account-id`.
- `account open-microsoft` — open Microsoft mail through browser OAuth. Optional: `--account-id`.
- `account upgrade-google-send --account` — grant Gmail send permission.
- `account upgrade-google-modify --account` — grant Gmail label and cloud-draft permission.
- `account upgrade-google-settings-basic --account` — grant Gmail filter and vacation permission.
- `account upgrade-microsoft-send --account` — grant Microsoft send permission.
- `account upgrade-microsoft-readwrite --account` — grant Microsoft folder and cloud-draft permission.
- `account reauthorize-google --account` — refresh an existing Gmail grant.
- `account reauthorize-microsoft --account` — refresh an existing Microsoft grant.
- `account reauthorize-imap --account` — replace a standard mailbox's credentials. `--credential-kind` is `password` or `app-password`; stdin carries two lines, IMAP credential first and SMTP credential second. Optional: `--credential-kind`.
- `account revoke-google --account` — revoke Gmail access and delete local credentials.
- `account revoke-microsoft --account` — revoke Microsoft access and delete local credentials.
- `account remove --account` — remove a standard mailbox locally.

### Search, read and track

- `mail mailboxes --account` — list folders of a standard mailbox.
- `mail search-intent` — search through the typed envelope; the default search entry. Stdin JSON such as `{"mailbox","filter","query","account","coverage"}`; see [search](references/search.md).
- `mail search --account` — low-level folder search. `--filter` is all, seen, unseen, from, subject or text; `--page-token` and `--page-size` (1 to 50) page the results. Optional: `--mailbox`, `--filter`, `--query`, `--page-token`, `--page-size`.
- `mail thread-intent` — list or read Gmail threads through the typed envelope; the default thread entry. Stdin JSON with `action` (`list` or `get`), `query` or `thread_id`, `format` and `account`; see [thread](references/thread.md).
- `mail thread-search --account` — low-level Gmail thread list. `--label` repeats; `--include-spam-trash` widens the search to Spam and Trash. Optional: `--query`, `--label`, `--page-token`, `--page-size`, `--include-spam-trash`.
- `mail thread-get --account --thread-id` — low-level Gmail thread read. `--format` is minimal, metadata or full; `--header` names headers and repeats. Optional: `--format`, `--header`.
- `mail history-intent` — check incremental Gmail changes through the typed envelope; the default history entry. Stdin JSON with `account` and optional `reset_cursor`; see [history](references/history.md).
- `mail history-list --account --start-history-id` — low-level Gmail change list. `--history-type` is messageAdded, messageDeleted, labelAdded or labelRemoved and repeats. Optional: `--label-id`, `--history-type`, `--page-token`, `--page-size`.
- `mail profile-get --account` — read the Gmail profile and current history id.
- `mail fetch --account` — read one message, identified by `--message-id` or by `--mailbox --uid-validity --uid`. Optional: `--message-id`, `--mailbox`, `--uid-validity`, `--uid`.
- `mail attachment --account --index` — download one attachment of a message. `--index` is the zero-based attachment number; identify the message as for `mail fetch`. Optional: `--message-id`, `--mailbox`, `--uid-validity`, `--uid`.
- `mail export --account --mailbox --uid-validity --uid` — export one message as raw MIME.
- `mail import --account` — import raw MIME as a local draft. Raw MIME bytes on stdin.

### Handling rules and memories

- `mail handling-list` — list confirmed pin and quiet rules.
- `mail handling-remember` — preview a rule. Stdin JSON `{"kind","match"}`; see [handling](references/handling.md). Optional: `--user-confirmed`.
- `mail handling-forget --rule-id --confirm` — forget a rule. `--confirm` must be `forget`.
- `mail memory-list` — list remember-me instructions.
- `mail memory-remember` — preview an instruction. Stdin JSON `{"text"}`; see [remember-me](references/remember-me.md). Optional: `--user-confirmed`.
- `mail memory-forget --memory-id --confirm` — forget an instruction. `--confirm` must be `forget`.

### Drafts and send

- `mail draft create --account` — create a local draft. Draft JSON on stdin; see [draft-send](references/draft-send.md).
- `mail draft reply --account --mailbox --uid-validity --uid` — create a reply draft. Draft JSON on stdin; see [draft-send](references/draft-send.md). Optional: `--reply-all`.
- `mail draft forward --account --mailbox --uid-validity --uid` — create a forward draft. Draft JSON on stdin; see [draft-send](references/draft-send.md).
- `mail draft read --account --draft-id` — show a draft and its frozen preview.
- `mail draft update --account --draft-id --expected-revision` — replace a draft. Draft JSON on stdin; see [draft-send](references/draft-send.md).
- `mail draft attach --account --draft-id --expected-revision` — add an attachment. Stdin JSON object with `id`, `filename`, `data_base64` and optional `content_type`.
- `mail draft detach --account --draft-id --expected-revision --attachment-id` — remove an attachment.
- `mail draft delete --account --draft-id --expected-revision` — delete a draft.
- `mail draft prepare --account --draft-id` — freeze the draft; returns the preview and a one-time challenge.
- `mail draft confirm-send --operation-id` — send once; the one-time challenge token is read from stdin.
- `mail draft dispatch-confirmed --operation-id` — resume a confirmed send that has not started dispatching.
- `mail draft status --operation-id` — show the state of one send.
- `mail draft push-cloud --account --draft-id` — upload a local draft to the provider's drafts folder. `--mailbox` defaults to Drafts. Optional: `--mailbox`.
- `mail draft cloud-list --account` — list Gmail cloud drafts.
- `mail draft cloud-delete --account --cloud-draft-id` — delete a Gmail cloud draft.
- `mail signature-get --account` — show the local signature.
- `mail signature-set --account` — set the local signature. UTF-8 signature text on stdin.
- `mail signature-clear --account` — clear the local signature.

### Organize and delete

- `mail flag --account --mailbox --uid-validity --uid --op` — change message flags. `--op` is add, remove or replace; `--flag` is one or more of seen, answered, flagged, deleted, draft. Optional: `--flag`.
- `mail move --account --to` — move a message. `--to` is the destination folder (or folder id on Microsoft); identify the message as for `mail fetch`. Optional: `--message-id`, `--mailbox`, `--uid-validity`, `--uid`.
- `mail copy --account --mailbox --uid-validity --uid --to` — copy a message to another folder.
- `mail archive --account --message-id` — archive a Gmail or Microsoft message.
- `mail junk --account --message-id` — move a Gmail or Microsoft message to Junk.
- `mail trash --account --mailbox --uid-validity --uid` — move a message to Trash. `--to` defaults to Trash. Optional: `--to`.
- `mail batch-trash --account --mailbox --uid-validity` — move many messages to Trash. `--uid` repeats once per message; `--to` defaults to Trash. Optional: `--uid`, `--to`.
- `mail restore --account --uid-validity --uid` — move a message out of Trash. `--mailbox` defaults to Trash and `--to` to INBOX. Optional: `--mailbox`, `--to`.
- `mail permanently-delete --account --mailbox --uid-validity --uid --confirm` — delete a message for good. `--confirm` must be `permanent-delete`.

### Gmail labels, filters and vacation

- `mail labels --account` — list labels.
- `mail label-get --account --label-id` — show one label.
- `mail label-create --account --name` — create a label.
- `mail label-update --account --label-id --name` — rename a label.
- `mail label-delete --account --label-id --confirm` — delete a label. `--confirm` must be `label-delete`.
- `mail label-modify --account --message-id` — add or remove labels on a message. Optional: `--add`, `--remove`.
- `mail filters --account` — list filters.
- `mail filter-get --account --filter-id` — show one filter.
- `mail filter-create --account` — create a filter. Optional: `--subject`, `--query`, `--add-label`.
- `mail filter-delete --account --filter-id --confirm` — delete a filter. `--confirm` must be `filter-delete`.
- `mail vacation-get --account` — show vacation auto-reply settings.
- `mail vacation-set --account` — change vacation settings. Stdin JSON `{"enable_auto_reply"}` (required true or false) with optional `response_subject` and `response_body_plain_text`; unknown fields are rejected.

### Microsoft folders

- `mail folder-create --account --name` — create a mail folder.
- `mail folder-rename --account --folder-id --name` — rename a folder.
- `mail folder-move --account --folder-id --destination-id` — move a folder under another parent.
- `mail folder-delete --account --folder-id --confirm` — delete a folder. `--confirm` must be `folder-delete`.

## Shared safety core

Gmail and Microsoft share the same confirmation model: local draft → exact
preview → one-time challenge on stdin → send status. Do not invent a second
confirmation path. Accepted Graph results may lack a provider message id;
report the durable `result` honestly and never auto-retry unknown outcomes.

## Updates

`anymail` may print one stderr line with schema `jz.plugin.update_notice.v1`. It never blocks a command. Finish what the user asked first, then tell them once, in plain words, that a newer version is available and ask whether to update now. On yes: refresh this plugin through the host (Claude Code: `claude plugin marketplace update jacky-plugins`, then `claude plugin update anymail@jacky-plugins`; Codex: `codex plugin marketplace upgrade jacky-plugins`, then `codex plugin add anymail@jacky-plugins`; other hosts: attach the new `plugins/anymail/` tree). Then run `doctor --repair-install` with the binary from the **new** package, not the folder this session started from (Claude Code: `~/.claude/plugins/cache/jacky-plugins/anymail/<latest>/bin/<platform>/anymail`; Codex: `~/.codex/plugins/cache/jacky-plugins/anymail/<latest>/bin/<platform>/anymail`; `<latest>` is the notice's `latest` value or the highest version folder present; `<platform>` is `darwin-arm64`, `linux-x64` or `win32-x64`). Read `references/whats-new.md` from that folder and tell the user in one or two sentences what changed and that the host loads it fully after a restart. For the rest of this session use that folder for every command and reference; the old version folder is gone. On no: do not ask again in this session.
