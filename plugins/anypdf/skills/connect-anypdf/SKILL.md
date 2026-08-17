---
name: connect-anypdf
description: Connect the public AnyPDF client once using the shared Portal user credential on stdin.
---

# Connect AnyPDF

## One shared user credential

- Every official product reuses `~/.jackyzhang.app/token/user.json` with `credential_kind=user` and `slot=user`.
- If that canonical slot already exists, run `anypdf whoami --json`. Do not ask for another Portal token merely because AnyPDF is being used for the first time.
- AnyPDF exchanges the durable `jz_` credential for a short-lived `aud=anypdf` JWT held only in memory. The raw credential is never sent to the AnyPDF API.
- Retired product credentials such as `ap_live_`, `ap_admin_`, or `fb_` are not valid Portal credentials.

## One-time local connection

The human runs the following command in their own terminal and enters the Portal credential at the non-echoing prompt:

```bash
anypdf login --json
```

Do not ask the human to paste the credential in chat, attach it, expose a local token file, or let an agent read or relay it. The CLI accepts the credential only through its bounded non-echoing stdin prompt and has no token argument. The secret is never placed in argv, shell history, stdout, stderr, a screenshot, or a report. A failed verification does not write `user.json`.

After connection, the agent may confirm with masked output from:

```bash
anypdf whoami --json
```

## Error meaning

- `token_invalid` means stdin did not contain one complete `jz_` credential. Never echo the submitted value.
- `auth_failed` means accountd or AnyPDF refused the credential. Check that the existing Portal **user** slot is active; do not change the person's role to work around it.
- `identity_invalid` means the installed AnyPDF client and service disagree about the `whoami` response. Do not ask the human to create another token. Update AnyPDF and report the product defect.

## Talk to the human

Say only whether AnyPDF connected and what the person can do next. If connection is needed, give the single local command above without asking for the credential itself. Do not expose token contents, internal HTTP, local paths, or raw JSON. A successful `whoami` reports only the user id, product role, scopes, form access, and expiry.
