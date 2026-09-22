# App-password / authorize-code path (standard mailboxes)

Use this when discovery says the mailbox needs an app password or a provider
authorize-code, not a raw account password typed into chat.

## Agent steps

1. Discover without credentials:

```bash
anymail account discover --email USER@EXAMPLE --json
```

2. Read `credential_requirement` from the JSON. When it is `app_password` (or
   the provider docs require an authorize-code / app password):

   - Tell the human, in everyday words, to open their provider security page
     and create a mailbox app password / authorize-code on their own device.
   - Never ask them to paste the secret into chat.
   - Place the secret only in the governed local secrets file used by AnyMail
     (development builds: the allowlisted temp or vault secrets path). The
     open command never accepts a password flag or stdin secret.

3. Open with the app-password credential class:

```bash
anymail account open --email USER@EXAMPLE --credential-kind app-password --json
```

4. Confirm readiness with `anymail account status --account ACCOUNT_ID --json`.

5. To remove the local binding later (standard IMAP/SMTP only):

```bash
anymail account remove --account ACCOUNT_ID --json
```

Gmail/Microsoft OAuth accounts use `revoke-google` / provider revoke flows,
not `account remove`.

## Talk to the human

Say they need a provider “应用专用密码 / app password / authorize code”, and
that AnyMail will use it only from local governed storage. Do not collect the
secret in the conversation. Do not ask for IMAP host, port, or TLS details
when discovery already returned a usable pair.
