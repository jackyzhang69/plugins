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
- This is a production OAuth misconfiguration, not a broken login
  command and not something the human can fix by being added as a
  tester. The shipped Gmail client is still on Google's Testing path, or
  the product is pointed at the internal development client.
- What the operator must do: marketplace AnyMail must already ship the
  **production** Google Desktop client. Put that client In production
  after Google verification, or rebake/republish if an old build still
  had the development client. Never add a real / production Gmail
  address (including a Tell Jacky reporter) to any test-user list.
- If they tapped Cancel on the consent screen, they can retry and allow
  access.
- What they should not do: paste tokens, client ids, or create their own
  OAuth app.

If the browser stays on Google's 403 page and never returns to AnyMail,
the CLI wait can time out with the same production-client next step. Do
not invent a different provider or scope. Do not ask the human to create
an OAuth app or paste a token.
