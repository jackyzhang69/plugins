---
name: connect-anypdf
description: Connect the public AnyPDF client once using a token supplied only on stdin.
---

# Connect AnyPDF

The public client defaults to `https://anypdf.jackyzhang.app`. To use another
backend for local development, set `ANYPDF_BACKEND_URL` for this invocation;
the URL is resolved each time and is never written into the credential file.

Tell the user to run this in their own terminal and enter the token at the
non-echoing prompt (the token is not a command-line argument or agent input):

```bash
anypdf login --json
```

Do not ask for the token in chat or put it in an agent tool call, pipe, argument,
shell history, prompt text, report, log, or output. The launcher reads it locally,
verifies the token with the backend, and then
writes only the token to `~/.jackyzhang.app/token/jz.json` using an atomic mode-0600 file
inside a mode-0700 directory. A failed verification never writes a credential.

After login, normal commands automatically reuse the saved credential:

```bash
anypdf whoami --json
anypdf doctor --json
anypdf forms catalog
```

`whoami` returns only public token metadata (id, type, scopes, form allowlist,
status, and expiry), never the raw token or owner secrets. `doctor` checks local
backend resolution and credential safety without making an unsolicited remote
request. `logout` removes only the saved local credential. The native client has no
environment credential override; the saved platform token is the only credential
source.
