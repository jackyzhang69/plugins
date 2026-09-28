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
