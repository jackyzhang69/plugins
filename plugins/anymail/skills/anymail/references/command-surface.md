<!-- generated-by: assert-skill-surface.py ; do not edit -->
# anymail command reference

Generated from `anymail commands --json` (version `0.1.15`). Every public command is listed.

## `anymail version`

Print the installed Anymail version as JSON

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail commands`

Print the implemented command surface as JSON

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail doctor`

Inspect local platform and install state as JSON

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--repair-install` | flag | no |  | Install this complete verified package at the canonical local path |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail login`

Persist the shared Portal jz_ from stdin into token/user.json

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--token-stdin` | flag | no |  | Read the credential from stdin. Never accepted on argv |
| `--force` | flag | no |  | Overwrite an existing valid user.json |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail logout`

Clear token/user.json

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail whoami`

Exchange aud=anymail and report a masked identity

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail feedback create`

Preview the exact feedback draft; submit it only after the user approves it

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--type` | option | no |  | Feedback type: feature-request, bug-report, or knowledge-tip |
| `--title` | option | no |  | Feedback title; do not include mailbox addresses |
| `--description` | option | no |  | Feedback description; do not include mailbox addresses |
| `--user-confirmed` | flag | no |  | Submit only after the user approves the exact preview |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail feedback list`

List the feedback reports sent to Jacky, from the service or the local mirror when offline

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail feedback status`

Show the status of one feedback report

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | no |  | Feedback id returned by feedback create or feedback list |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail feedback inbox`

List unread updates Jacky posted on feedback reports

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail feedback read`

Mark one feedback update as read

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--update-id` | option | no |  | Update id shown by feedback inbox |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account list`

List durable local mailbox accounts without contacting providers

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account discover`

Discover and preflight a standard IMAP/SMTP account without credentials

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--email` | option | yes |  | Mailbox address used for typed discovery |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account open`

Discover and open a standard IMAP/SMTP account. It never accepts a password, so a shipped build always stops with an error and cannot open one

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--email` | option | yes |  | Mailbox address used for typed discovery |
| `--account-id` | option | no |  | Stable local account label. Defaults to the mailbox address |
| `--read-login` | option | no |  | Override the discovered IMAP login name |
| `--send-login` | option | no |  | Override the discovered SMTP login name |
| `--from` | option | no |  | Envelope From address used for the SMTP verification probe |
| `--credential-kind` | option | no | `password`, `app-password` | Credential class for the standard password path |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account open-google`

Open one Gmail account through the native browser OAuth flow

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account-id` | option | no |  | Stable local account label. Defaults to the verified Gmail address |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account open-microsoft`

Open one Microsoft account through the native browser OAuth flow

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account-id` | option | no |  | Stable local account label. Defaults to the verified mailbox address |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account upgrade-google-send`

Explicitly upgrade one Gmail account with the minimum send OAuth grant. Google may show an unverified-app or unsafe warning for this sensitive send scope; that is Google's consent interstitial until verification completes, not an AnyMail defect

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Stable local account label of an already-open Gmail account |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account upgrade-google-modify`

Explicitly upgrade one Gmail account with gmail.modify for label mutation

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Stable local account label of an already-open Gmail account |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account upgrade-google-settings-basic`

Explicitly upgrade one Gmail account with gmail.settings.basic for filters/vacation

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Stable local account label of an already-open Gmail account |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account upgrade-microsoft-send`

Explicitly upgrade one Microsoft account with the Mail.Send grant

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Stable local account label of an already-open Microsoft account |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account upgrade-microsoft-readwrite`

Explicitly upgrade one Microsoft account with Mail.ReadWrite for cloud drafts

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Stable local account label of an already-open Microsoft account |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account reauthorize-google`

Reauthorize an existing active Gmail account with its exact stored OAuth grant while preserving its local binding and drafts

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Stable local account label of an already-configured Gmail account |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account reauthorize-microsoft`

Reauthorize an existing Microsoft account with its exact saved OAuth grant while preserving its local binding and drafts

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Stable local account label of an already-configured Microsoft account |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account reauthorize-imap`

Replace an active standard account's password credentials after exact endpoint re-discovery. Read two fresh secrets from nonterminal stdin

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Stable local account label of an already-configured IMAP account |
| `--credential-kind` | option | no | `password`, `app-password` | Credential class for the standard password path |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account revoke-google`

Revoke one Gmail account: call Google token revoke, delete local credentials, and tombstone the durable binding

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Stable local account label of an already-configured Gmail account |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account revoke-microsoft`

Revoke one Microsoft account: call Graph revokeSignInSessions, delete local credentials, and tombstone the durable binding

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Stable local account label of an already-configured Microsoft account |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account remove`

Remove one standard IMAP/SMTP account: delete local credentials and tombstone the durable binding. Gmail accounts must use revoke-google

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Stable local account label of an already-configured standard account |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail account status`

Read one durable local mailbox account without contacting providers

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Stable local account label |
| `--json` | flag | no |  | Select the machine-readable output contract |

## `anymail mail mailboxes`

List folders through a read-only IMAP session

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |

## `anymail mail search`

Search one folder by UID without changing Seen flags

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--mailbox` | option | no |  | Folder to search; defaults to INBOX |
| `--filter` | option | no | `all`, `seen`, `unseen`, `from`, `subject`, `text` | Which part of the message the query matches |
| `--query` | option | no |  | Text to look for in the selected part of the message |
| `--page-token` | option | no |  | Opaque continuation token from a prior search page |
| `--page-size` | option | no |  | Page size (1..=50). Defaults to the provider page size |

## `anymail mail search-intent`

Search one folder through the typed readiness envelope contract

No arguments.

## `anymail mail thread-search`

List Gmail conversation threads for one account

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--query` | option | no |  | Gmail search query |
| `--label` | option | no |  | Only threads carrying this Gmail label; repeatable |
| `--page-token` | option | no |  | Opaque continuation token from a prior page |
| `--page-size` | option | no |  | Page size; defaults to the provider page size |
| `--include-spam-trash` | flag | no |  | Include threads in Spam and Trash |

## `anymail mail thread-get`

Read one Gmail conversation thread

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--thread-id` | option | yes |  | Gmail thread id |
| `--format` | option | no | `minimal`, `metadata`, `full` | How much of each message to return |
| `--header` | option | no |  | Header names to include when the format is metadata; repeatable |

## `anymail mail history-list`

List mailbox changes since a saved history cursor

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--start-history-id` | option | yes |  | History id to list changes after |
| `--label-id` | option | no |  | Only changes affecting this Gmail label id |
| `--history-type` | option | no | `messageAdded`, `messageDeleted`, `labelAdded`, `labelRemoved` | Change kinds to include; repeatable |
| `--page-token` | option | no |  | Opaque continuation token from a prior page |
| `--page-size` | option | no |  | Page size; defaults to the provider page size |

## `anymail mail profile-get`

Read mailbox profile facts needed to start incremental sync

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |

## `anymail mail thread-intent`

List or read Gmail threads through the typed readiness envelope

No arguments.

## `anymail mail history-intent`

Read incremental mailbox changes through the typed readiness envelope

No arguments.

## `anymail mail handling-list`

List confirmed AnyMail handling rules from the server

No arguments.

## `anymail mail handling-remember`

Remember one handling rule after the human confirms the exact draft

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--user-confirmed` | flag | no |  | Must be set only after the human approved the exact draft text |

## `anymail mail handling-forget`

Forget one handling rule after explicit confirmation

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--rule-id` | option | yes |  | Id of the handling rule to forget |
| `--confirm` | option | yes |  | Must equal `forget` |

## `anymail mail memory-list`

List confirmed AnyMail remember-me short instructions

No arguments.

## `anymail mail memory-remember`

Remember one short instruction after the human confirms the exact text

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--user-confirmed` | flag | no |  | Must be set only after the human approved the exact draft text |

## `anymail mail memory-forget`

Forget one remember-me memory after explicit confirmation

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--memory-id` | option | yes |  | Id of the remember-me memory to forget |
| `--confirm` | option | yes |  | Must equal `forget` |

## `anymail mail fetch`

Fetch one UID and a bounded parsed text projection

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--message-id` | option | no |  | Provider message id for Gmail or Microsoft Graph |
| `--mailbox` | option | no |  | Mailbox (folder) name |
| `--uid-validity` | option | no |  | UIDVALIDITY of the mailbox, as returned with the message |
| `--uid` | option | no |  | Message UID within the mailbox; required unless a provider message id is given |

## `anymail mail attachment`

Retrieve one bounded MIME attachment as base64 JSON

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--message-id` | option | no |  | Provider message id for Gmail or Microsoft Graph |
| `--mailbox` | option | no |  | Mailbox (folder) name |
| `--uid-validity` | option | no |  | UIDVALIDITY of the mailbox, as returned with the message |
| `--uid` | option | no |  | Message UID within the mailbox |
| `--index` | option | yes |  | Zero-based index of the attachment in the message |

## `anymail mail flag`

Add, remove, or replace IMAP system flags on one UID

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--mailbox` | option | yes |  | Mailbox (folder) name |
| `--uid-validity` | option | yes |  | UIDVALIDITY of the mailbox, as returned with the message |
| `--uid` | option | yes |  | Message UID within the mailbox |
| `--op` | option | yes | `add`, `remove`, `replace` | Whether to add or remove the flags |
| `--flag` | option | no | `seen`, `answered`, `flagged`, `deleted`, `draft` | Message flags to change; repeatable |

## `anymail mail move`

Move one IMAP UID into another mailbox (UID MOVE), or one Graph message into a well-known / folder id destination

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--message-id` | option | no |  | Provider message id for Microsoft Graph |
| `--mailbox` | option | no |  | Mailbox (folder) name |
| `--uid-validity` | option | no |  | UIDVALIDITY of the mailbox, as returned with the message |
| `--uid` | option | no |  | Message UID within the mailbox |
| `--to` | option | yes |  | Destination folder name |

## `anymail mail copy`

Copy one IMAP UID into another mailbox (UID COPY)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--mailbox` | option | yes |  | Mailbox (folder) name |
| `--uid-validity` | option | yes |  | UIDVALIDITY of the mailbox, as returned with the message |
| `--uid` | option | yes |  | Message UID within the mailbox |
| `--to` | option | yes |  | Destination folder name |

## `anymail mail draft create`

Create a plain-text draft from bounded JSON on stdin

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |

## `anymail mail draft reply`

Create a reply draft from one fetched message and bounded JSON on stdin

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--mailbox` | option | yes |  | Mailbox (folder) name |
| `--uid-validity` | option | yes |  | UIDVALIDITY of the mailbox, as returned with the message |
| `--uid` | option | yes |  | Message UID within the mailbox |
| `--reply-all` | flag | no |  | Reply to all recipients instead of only the sender |

## `anymail mail draft forward`

Create a forward draft from one fetched message and bounded JSON on stdin

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--mailbox` | option | yes |  | Mailbox (folder) name |
| `--uid-validity` | option | yes |  | UIDVALIDITY of the mailbox, as returned with the message |
| `--uid` | option | yes |  | Message UID within the mailbox |

## `anymail mail draft push-cloud`

APPEND one local draft into an IMAP mailbox (default Drafts), or create a Gmail cloud draft when the account is Google with `gmail.modify`

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--draft-id` | option | yes |  | Local draft id |
| `--mailbox` | option | no |  | Provider drafts folder to upload into; defaults to Drafts |

## `anymail mail draft cloud-list`

List Gmail cloud drafts (requires gmail.modify)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |

## `anymail mail draft cloud-delete`

Delete one Gmail cloud draft by id (requires gmail.modify)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--cloud-draft-id` | option | yes |  | Provider id of the cloud draft to delete |

## `anymail mail draft read`

Read one local draft and return its exact frozen preview

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--draft-id` | option | yes |  | Local draft id |

## `anymail mail draft update`

Replace one local draft after an exact revision check

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--draft-id` | option | yes |  | Local draft id |
| `--expected-revision` | option | yes |  | Draft revision the caller last saw; the change is rejected if the draft moved on |

## `anymail mail draft attach`

Append one attachment onto an existing local draft (C10)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--draft-id` | option | yes |  | Local draft id |
| `--expected-revision` | option | yes |  | Draft revision the caller last saw; the change is rejected if the draft moved on |

## `anymail mail draft detach`

Remove one attachment from an existing local draft by id (C10)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--draft-id` | option | yes |  | Local draft id |
| `--expected-revision` | option | yes |  | Draft revision the caller last saw; the change is rejected if the draft moved on |
| `--attachment-id` | option | yes |  | Id of the attachment to remove from the draft |

## `anymail mail draft delete`

Delete one local draft after an exact revision check

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--draft-id` | option | yes |  | Local draft id |
| `--expected-revision` | option | yes |  | Draft revision the caller last saw; the change is rejected if the draft moved on |

## `anymail mail draft prepare`

Freeze a draft and return a structured preview and one-time challenge

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--draft-id` | option | yes |  | Local draft id |

## `anymail mail draft confirm-send`

Consume an explicit challenge token from stdin, then send once

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--operation-id` | option | yes |  | Operation id returned by `mail draft prepare` or `mail draft confirm-send` |

## `anymail mail draft dispatch-confirmed`

Resume a confirmed operation only if dispatch has not started

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--operation-id` | option | yes |  | Operation id returned by `mail draft prepare` or `mail draft confirm-send` |

## `anymail mail draft status`

Inspect the durable state of one send operation

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--operation-id` | option | yes |  | Operation id returned by `mail draft prepare` or `mail draft confirm-send` |

## `anymail mail trash`

Soft-delete one IMAP UID by moving it into Trash

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--mailbox` | option | yes |  | Mailbox (folder) name |
| `--uid-validity` | option | yes |  | UIDVALIDITY of the mailbox, as returned with the message |
| `--uid` | option | yes |  | Message UID within the mailbox |
| `--to` | option | no |  | Trash folder name; defaults to Trash |

## `anymail mail archive`

Archive one Gmail/Graph message (remove from inbox / move to Archive)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--message-id` | option | yes |  | Provider message id |

## `anymail mail junk`

Move one Gmail/Graph message to Junk/Spam

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--message-id` | option | yes |  | Provider message id |

## `anymail mail batch-trash`

Soft-delete many IMAP UIDs; reports per-uid success and failure

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--mailbox` | option | yes |  | Mailbox (folder) name |
| `--uid-validity` | option | yes |  | UIDVALIDITY of the mailbox, as returned with the message |
| `--uid` | option | no |  | Message UIDs to move to trash; repeatable |
| `--to` | option | no |  | Trash folder name; defaults to Trash |

## `anymail mail restore`

Restore one IMAP UID by moving it out of Trash into a mailbox

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--mailbox` | option | no |  | Folder the message is in now; defaults to Trash |
| `--uid-validity` | option | yes |  | UIDVALIDITY of the mailbox, as returned with the message |
| `--uid` | option | yes |  | Message UID within the mailbox |
| `--to` | option | no |  | Folder to restore the message into; defaults to INBOX |

## `anymail mail permanently-delete`

Permanently delete one IMAP UID after an explicit confirmation token

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--mailbox` | option | yes |  | Mailbox (folder) name |
| `--uid-validity` | option | yes |  | UIDVALIDITY of the mailbox, as returned with the message |
| `--uid` | option | yes |  | Message UID within the mailbox |
| `--confirm` | option | yes |  | Must equal `permanent-delete` to proceed |

## `anymail mail export`

Export one IMAP UID as bounded base64 raw MIME

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--mailbox` | option | yes |  | Mailbox (folder) name |
| `--uid-validity` | option | yes |  | UIDVALIDITY of the mailbox, as returned with the message |
| `--uid` | option | yes |  | Message UID within the mailbox |

## `anymail mail import`

Import one bounded raw MIME message as a local draft

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |

## `anymail mail labels`

List Gmail labels (C14 native surface) for one Google account

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |

## `anymail mail label-create`

Create one Gmail user label (requires gmail.modify upgrade)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--name` | option | yes |  | Name of the new label |

## `anymail mail label-get`

Get one Gmail label by id

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--label-id` | option | yes |  | Provider label id |

## `anymail mail label-update`

Rename one Gmail user label (requires gmail.modify upgrade)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--label-id` | option | yes |  | Provider label id |
| `--name` | option | yes |  | New label name |

## `anymail mail label-delete`

Delete one Gmail user label (requires gmail.modify + confirm token)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--label-id` | option | yes |  | Provider label id |
| `--confirm` | option | yes |  | Must equal `label-delete` to proceed |

## `anymail mail label-modify`

Add/remove Gmail labels on one message (requires gmail.modify upgrade)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--message-id` | option | yes |  | Provider message id |
| `--add` | option | no |  | Label ids to add; repeatable |
| `--remove` | option | no |  | Label ids to remove; repeatable |

## `anymail mail filters`

List Gmail filters (requires gmail.settings.basic upgrade)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |

## `anymail mail filter-get`

Get one Gmail filter by id (requires gmail.settings.basic)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--filter-id` | option | yes |  | Provider filter id |

## `anymail mail filter-create`

Create one Gmail filter (requires gmail.settings.basic)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--subject` | option | no |  | Match messages whose subject contains this text |
| `--query` | option | no |  | Match messages that match this Gmail query |
| `--add-label` | option | no |  | Label ids the filter adds to matching messages; repeatable |

## `anymail mail filter-delete`

Delete one Gmail filter (requires gmail.settings.basic + confirm)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--filter-id` | option | yes |  | Provider filter id |
| `--confirm` | option | yes |  | Must equal `filter-delete` to proceed |

## `anymail mail vacation-get`

Get Gmail vacation auto-reply settings (requires gmail.settings.basic)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |

## `anymail mail vacation-set`

Update Gmail vacation settings from bounded JSON stdin (requires settings.basic)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |

## `anymail mail folder-create`

Create one Microsoft Graph mail folder (requires Mail.ReadWrite)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--name` | option | yes |  | Name of the new folder |

## `anymail mail folder-rename`

Rename one Microsoft Graph mail folder (requires Mail.ReadWrite)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--folder-id` | option | yes |  | Provider folder id |
| `--name` | option | yes |  | New folder name |

## `anymail mail folder-move`

Move one Microsoft Graph mail folder under another parent

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--folder-id` | option | yes |  | Provider folder id |
| `--destination-id` | option | yes |  | Id of the folder to move into |

## `anymail mail folder-delete`

Delete one Microsoft Graph mail folder (requires Mail.ReadWrite + confirm)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
| `--folder-id` | option | yes |  | Provider folder id |
| `--confirm` | option | yes |  | Must equal `folder-delete` to proceed |

## `anymail mail signature-get`

Get the durable local signature text for one account (C08)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |

## `anymail mail signature-set`

Set the durable local signature text from stdin (C08)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |

## `anymail mail signature-clear`

Clear the durable local signature file for one account (C08)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--account` | option | yes |  | Local account id from `account list` |
