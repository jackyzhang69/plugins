# Open a mailbox account

AnyMail opens official Google/Microsoft OAuth accounts and standard
IMAP/SMTP password or app-password accounts discovered automatically.

## Agent steps

1. Prefer an existing ready account from `anymail account list --json`.
2. If the user wants Gmail and none is ready:

```bash
anymail account open-google --json
```

3. If the user wants Microsoft / Outlook and none is ready:

```bash
anymail account open-microsoft --json
```

4. If the user wants a standard provider (for example Fastmail, or another
   discovered IMAP/SMTP pair):

```bash
anymail account discover --email USER@EXAMPLE --json
anymail account open --email USER@EXAMPLE --json
```

When discovery requires an app password / authorize-code, follow
[app-password](app-password.md).

5. Send capability for OAuth accounts is separate. Only when the user clearly
   wants to send:

```bash
anymail account upgrade-google-send --account ACCOUNT_ID --json
anymail account upgrade-microsoft-send --account ACCOUNT_ID --json
```

Before `upgrade-google-send`, tell the human in everyday words: Google
may show an **unverified-app** or **unsafe** warning when AnyMail asks
for send permission, even if read-only access already worked. That
screen is Google's consent interstitial for the sensitive Gmail send
scope, not an AnyMail crash and not proof the CLI is malware. They
should continue only if they trust this AnyMail install, and use
**Advanced** / proceed if Google offers that path. The warning stays
until the operator finishes Google Cloud verification for the shipped
send scopes. **In production** publishing status alone does not
complete that verification. Do not ask them to create an OAuth app or
paste secrets.

## Reauthorize an existing mailbox

Use reauthorization for an account that is already configured. It refreshes
that account's authorization or credentials without opening a second account.

For an existing Microsoft / Outlook OAuth account:

```bash
anymail account reauthorize-microsoft --account ACCOUNT_ID --json
```

This opens the official browser flow for the account's saved OAuth grant and
preserves its local binding and drafts. Ask the human to complete any provider
consent in the browser; never request a token or client secret in chat.

For an existing standard IMAP/SMTP account whose credentials need replacement:

```bash
anymail account reauthorize-imap --account ACCOUNT_ID --credential-kind password --json
```

Use `--credential-kind app-password` when the replacement credentials are an
app password / authorize-code. The command reads exactly two fresh,
newline-terminated UTF-8 lines from nonterminal stdin, with the read (IMAP)
credential first and send (SMTP) credential second, followed by EOF. It rejects
interactive terminal input and credentials on argv. Pass both values only via
a secure local stdin path. Never ask for them in chat or place them in shell
arguments or logs. If secure stdin is unavailable, stop and request an approved
secure input path. The command rediscovers the provider endpoints and proceeds
only if they still match the saved account.

6. Remove a standard IMAP/SMTP account locally:

```bash
anymail account remove --account ACCOUNT_ID --json
```

Gmail revoke:

```bash
anymail account revoke-google --account ACCOUNT_ID --json
```

## Talk to the human

For OAuth, explain that a browser window will ask them to choose the account
and allow AnyMail. For app-password providers, follow the app-password
playbook: never collect the secret in chat.

## Gmail Error 403 / access_denied

If `account open-google` fails with `access_denied`, or the browser shows
Google **Error 403** / "this app is being tested" / an unverified-app
warning, say this in everyday words:

- AnyMail asked Google for permission and Google refused.
- The screen alone does not identify the cause. A client still in Testing,
  an unverified app or requested Gmail scope, or a different configured
  client can each require a different operator action. Publishing status
  **In production** does not itself complete Google's verification.
- The operator checks the actual shipped Desktop client and the Google
  Cloud OAuth publishing, branding, and data-access verification states,
  then resolves the specific issue shown there. Never add a real /
  production Gmail address (including a Tell Jacky reporter) to a
  test-user list.
- If they tapped Cancel on the consent screen, they can retry and allow
  access.
- What they should not do: paste tokens, client ids, or create their own
  OAuth app.

If the browser stays on Google's 403 page and never returns to AnyMail,
the CLI wait can time out. Check the same OAuth status and verification
evidence. Do not invent a different provider or scope. Do not ask the
human to create an OAuth app or paste a token.

## Gmail identity claims / incomplete grant

If `account open-google` returns after the browser and fails with
**OIDC identity claims are invalid** or **complete Gmail identity
grant**, the local account was not opened or changed.

Say this in everyday words:

- Google returned a sign-in that AnyMail could not accept. Invalid
  claims stay rejected; do not bypass the check.
- `account list` can still show `configured` / `active`. That is the
  last stored authority, not a live Gmail read. Mail search can then
  fail while the list still looks healthy.
- If the browser warned that AnyMail is unsafe or unverified, the
  operator checks the shipped Desktop client's Google Cloud publishing
  and verification status. **In production** does not finish
  verification or guarantee a usable identity grant. Never add a real /
  production Gmail address (including a Tell Jacky reporter) to a
  test-user list.
- For an account that is already configured, recover with
  `anymail account reauthorize-google --account ACCOUNT_ID` after that
  operator check. Reauthorization verifies the same Google identity,
  current OAuth registration, and recorded grant, and keeps the
  account binding and local drafts.
- What they should not do: copy credential files, remove and reopen
  the account to skip the check, paste tokens or client ids, or create
  their own OAuth app.

## Gmail send-consent unverified / unsafe warning

If `account upgrade-google-send` opens Google and the human sees
"unverified", "this app isn't verified", or **unsafe**, say:

- Read-only Gmail access can succeed while send still shows this
  screen, because `gmail.send` is a separate sensitive scope.
- The warning is Google's interstitial. AnyMail cannot hide or click
  it. Continuing is a trust choice, not a CLI bypass.
- The durable fix is operator-side: finish Google Cloud verification
  for the shipped send scopes on the production Desktop client.
  Publishing **In production** without that verification still shows
  the warning.
- If they cancel, the CLI reports that Google did not grant access and
  repeats the same send-scope note. Retry only if they still want send.
