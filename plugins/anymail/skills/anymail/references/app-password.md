# Standard mailboxes (IMAP/SMTP, password or app password)

A standard IMAP/SMTP mailbox cannot be opened in the published plugin. Tell
the user this plainly and offer Gmail or Outlook instead
([open-account](open-account.md)).

## What the product does

- `anymail account discover --email USER@EXAMPLE --json` is read-only. It
  reports what a standard mailbox would use, including whether it needs an
  app password (`credential_requirement`), and changes nothing. It is safe to
  run to answer "could you open this address?".
- `anymail account open --email USER@EXAMPLE --json` (also with
  `--credential-kind password` or `app-password`) never accepts a password
  from the user: not argv, stdin, environment, a prompt or an OS keychain.
  After discovery it looks for the password in a developer-only secret
  source that is compiled out of every shipped build, so it always stops with
  an error. A published build stops with "credential storage is unavailable"
  on macOS and Windows; on Linux it stops earlier with "account state storage
  is unavailable" (see "Platform limits" in [open-account](open-account.md)).
  Do not look for another channel and do not invent one. Never ask the human
  for the password or app password, and never write it into a file.
- `anymail account reauthorize-imap --account ACCOUNT_ID` reads two fresh
  credential lines from nonterminal stdin (IMAP first, SMTP second), but it
  only replaces the credentials of a standard account that already exists
  and whose saved endpoints and logins still match. It cannot create an
  account.

Optional flags of `account open`, for reference: `--read-login` and
`--send-login` are the IMAP and SMTP login names (not secrets) and replace
the username discovery found; the default is discovery's username, else the
email. At least one of them must equal the email, otherwise the command is
rejected as invalid input. `--from` is the sender address for outgoing mail;
it defaults to the email and must be a valid address. `--account-id` is the
local label and defaults to the email.

## Agent steps

1. If the user asks for a standard mailbox, optionally run `account discover`
   to confirm it, then say in everyday words that this release cannot open
   that kind of mailbox yet.
2. Offer Gmail (`anymail account open-google --json`) or Outlook
   (`anymail account open-microsoft --json`) if the user has such an account.
3. For a standard account that already exists, `anymail account status
   --account ACCOUNT_ID --json` shows its state, and
   `anymail account remove --account ACCOUNT_ID --json` removes the local
   binding. Gmail and Microsoft accounts use `revoke-google` /
   `revoke-microsoft`, not `account remove`.

## Talk to the human

Say that standard (IMAP/SMTP) mailboxes are not available in this release and
that Gmail and Outlook are. Do not ask for a password or app password, and do
not ask for IMAP host, port, or TLS details.
