---
name: connect-anyimmi
description: Connect the public AnyImmi client once using a token supplied only on stdin.
---

# Connect AnyImmi

The public client connects securely to `https://es_search.jackyzhang.app` over HTTPS.

Tell the user to run this in their own terminal and enter the token at the non-echoing prompt (the token is not a command-line argument or agent input):

```bash
anyimmi login
```

Do not ask for the token in chat or put it in an agent tool call, pipe, argument, shell history, prompt text, report, log, or output. The launcher reads it locally, and writes only the token to `~/.jackyzhang.app/token/user.json` using an atomic mode-0600 file inside a mode-0700 directory.

After login, normal commands automatically reuse the saved credential:

```bash
anyimmi whoami --json
anyimmi doctor --json
```

`whoami` returns only masked token metadata, never raw tokens. `doctor` checks local backend resolution and credential safety. `logout` removes the saved local credential.
