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
