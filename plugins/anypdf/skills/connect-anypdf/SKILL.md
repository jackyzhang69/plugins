---
name: connect-anypdf
description: Connect the public AnyPDF client once using a canonical credential supplied only on stdin.
---

# Connect AnyPDF

The public client defaults to `https://anypdf.jackyzhang.app`. To use another
backend for local development, set `ANYPDF_BACKEND_URL` for this invocation;
the URL is resolved each time and is never written into the credential slot.

Tell the user to run this in their own terminal and enter the credential at the
non-echoing prompt (the value is not a command-line argument or agent input):

```bash
anypdf login --json
```

Do not ask for the credential in chat or put it in an agent tool call, pipe,
argument, shell history, prompt text, report, log, or output. The launcher reads
stdin, verifies it through accountd, and atomically writes only the mode-0600
canonical user slot `~/.jackyzhang.app/token/user.json` inside a mode-0700
directory. A failed verification never writes a slot.

Subsequent commands exchange the user slot for a short-lived exact-audience JWT
held only in process memory; the durable value is never sent to the AnyPDF
product API:

```bash
anypdf whoami --json
anypdf doctor --json
anypdf forms catalog
```

`whoami` returns only public token metadata (id, type, scopes, form allowlist,
status, and expiry), never the raw token or owner secrets. `doctor` checks local
backend resolution and credential safety without making an unsolicited remote
request. `logout` removes only the saved user slot. The native client has no
environment credential override.
