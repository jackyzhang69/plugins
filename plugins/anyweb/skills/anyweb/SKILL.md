---
name: anyweb
description: >-
  READ THIS FIRST for AnyWeb. Check whether an asynchronous website repair is
  queued, needs reproduction input, or has a verified successor available.
  Configure, inspect, or forget optional hosted website continuity without
  revealing a stored password or security answer.
  Connect or log in with the shared Portal token, check identity and doctor,
  and Tell Jacky with a feature request, bug report, or knowledge tip. This
  release reports repair outcomes; it is not a general web automation tool.
when_to_use: |-
  Load on plugin start. Trigger phrases: "check my AnyWeb repair",
  "is the website repair ready", "connect or log in to AnyWeb",
  "save my Portal token for AnyWeb", "Tell Jacky about AnyWeb",
  "set up hosted website login", "forget my hosted website login",
  "answer this saved security question",
  "report an AnyWeb bug", "request an AnyWeb feature",
  "send an AnyWeb knowledge tip".
---

# AnyWeb — host agent contract

Ask the live CLI, never memory of an older release:

```bash
"$ANYWEB_BIN" commands --json
```

Playbooks: [connect](references/connect.md), [hosted continuity](references/hosted-continuity.md),
[repair claims](references/repair.md), and [Tell Jacky](references/tell-jacky.md).

Public commands are `doctor`, `login`, `logout`, `whoami`, `repair claims`,
`hosted-account set`, `hosted-account status`, `hosted-account forget`,
`hosted-security-answer set`, `hosted-security-answer status`,
`hosted-security-answer forget`,
`feedback create`, `feedback list`, `feedback status`, `feedback inbox`,
`feedback read`, and `commands`.

Resolve `$ANYWEB_BIN` from the current plugin package for this host. On macOS
Apple Silicon it is `bin/darwin-arm64/anyweb`; on Windows x64 it is
`bin/win32-x64/anyweb.exe`. If the canonical current install is absent, run the
package binary's `doctor --repair-install --json`, then use
`~/.jackyzhang.app/plugins/anyweb/current/...`. Never choose a random PATH
binary over the package currently loaded by the host.

Runtime data stays in `~/.jackyzhang.app/anyweb/`; the shared Portal identity
stays in `~/.jackyzhang.app/token/user.json`. Local website custody remains the
default. Hosted website custody is separate, optional, explicitly confirmed,
write-only, replaceable, and forgettable. Never expose repair refs as website
content, and never treat `repair_available` as permission to submit,
sign, pay, upload, send, withdraw, delete, or make a final declaration.

## Talk to the human

Use plain language. Report the state and what it means, not raw JSON or flags.
Never claim that AnyWeb can execute arbitrary website work in this release.
Never claim an application was submitted, complete, or ready to file.
