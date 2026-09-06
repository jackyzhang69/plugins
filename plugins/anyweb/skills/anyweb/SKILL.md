---
name: anyweb
description: >-
  READ THIS FIRST for AnyWeb. Check whether an asynchronous website repair is
  queued, needs reproduction input, or has a verified successor available.
  Configure, inspect, or forget optional hosted website continuity without
  revealing a stored password or security answer.
  Run supported bounded website tasks in visible Chrome on the user's own
  computer, with structured pause, status, resume, cancel, and result states.
  Connect or log in with the shared Portal token, check identity and doctor,
  and Tell Jacky with a feature request, bug report, or knowledge tip.
  Connect with Jacky, start a pair session, or use a join code so Jacky's
  assistant can look at this machine's AnyWeb status. This is
  not a general web automation tool.
when_to_use: |-
  Load on plugin start. Trigger phrases: "check my AnyWeb repair",
  "is the website repair ready", "connect or log in to AnyWeb",
  "save my Portal token for AnyWeb", "Tell Jacky about AnyWeb",
  "set up hosted website login", "forget my hosted website login",
  "answer this saved security question",
  "continue this supported website draft on my computer",
  "create or continue my Express Entry profile",
  "fill my EE profile up to the final review step",
  "check or resume my AnyWeb task",
  "report an AnyWeb bug", "request an AnyWeb feature",
  "send an AnyWeb knowledge tip",
  "connect with Jacky", "pair session", "join code from Jacky".
---

# AnyWeb — host agent contract

Load this on plugin start and whenever the user asks about website repair,
hosted continuity, supported local tasks, connect, pair session, or Tell Jacky.

## Talk to the human

Use plain language. Report the state and what it means, not raw JSON or flags.
Never claim that AnyWeb can execute arbitrary website work in this release.
Never claim an application was submitted, complete, or ready to file.
For the supported Express Entry task, say clearly that AnyWeb stops before the
final Continue/submission boundary and never submits the profile. On the first
session after install, read [references/get-started.md](references/get-started.md)
before your first user-visible reply. After a version update, read
[references/whats-new.md](references/whats-new.md) and resume the prior intent
without asking the human to repeat it. Never ask the human to run doctor as
homework. Run the intended ordinary command first; when stdout is
`jz.plugin.envelope.v1`, follow its exact `status` and `continue_args` until
the product reports `ready`, then resume the sealed request.

## When the user asks "what can you do?"

Do not answer from a frozen command list in this file. Run the live client and
translate `commands --json` into product language:

```bash
"$ANYWEB_BIN" commands --json
```

If the user is not connected, run [connect](references/connect.md) first.

## Agent router — intents

| User intent | Host does | Human may be asked |
|---|---|---|
| "what can AnyWeb do" | Live `"$ANYWEB_BIN" commands --json`; translate to supported repair, task, and continuity capabilities | Connect once if not logged in ([connect](references/connect.md)) |
| check my AnyWeb repair / is the website repair ready | [repair](references/repair.md): `repair claims --json`; translate queue status | Facts to reproduce a blocked repair when the product asks |
| connect or log in to AnyWeb / save my Portal token | [connect](references/connect.md): pipe Portal token via stdin | Token file path or one-time paste (never argv) |
| set up hosted website login / forget my hosted website login / answer this saved security question | [hosted continuity](references/hosted-continuity.md) | Explicit confirm before storing or replacing hosted credentials; security answers only when the product requests them |
| continue this supported website draft / create or continue my Express Entry profile / check or resume my AnyWeb task | [local tasks](references/tasks.md): bounded visible Chrome task lifecycle | Facts the site needs; confirm before sensitive steps; verify outcome matches expectation |
| Tell Jacky about AnyWeb / report an AnyWeb bug / request an AnyWeb feature / send an AnyWeb knowledge tip | [Tell Jacky](references/tell-jacky.md) | Confirm the exact draft before send |
| connect with Jacky / pair session / join code from Jacky | [pair-session](references/pair-session.md) | Confirm once that Jacky's assistant may look at this machine's AnyWeb repair, task, or continuity status |

Playbooks: [connect](references/connect.md), [hosted continuity](references/hosted-continuity.md),
[local tasks](references/tasks.md), [repair claims](references/repair.md),
[Tell Jacky](references/tell-jacky.md), and
[pair-session](references/pair-session.md).

## Live CLI discovery (fail-closed)

Ask the live CLI, never from memory of an older release:

```bash
"$ANYWEB_BIN" commands --json
```

Command path reference (not for first-session orientation):
[references/command-surface.md](references/command-surface.md).

Resolve `$ANYWEB_BIN` from the current plugin package for this host. On macOS
Apple Silicon it is `bin/darwin-arm64/anyweb`; on Windows x64 it is
`bin/win32-x64/anyweb.exe`. If the canonical current install is absent, run the
package binary's `doctor --repair-install --json`, then use
`~/.jackyzhang.app/plugins/anyweb/current/...`. Never choose a random PATH
binary over the package currently loaded by the host.

`runtime doctor` is read-only. If it reports `runtime_missing`, run the exact
`runtime install --json` continuation, then run `runtime doctor --json` again.
The install command downloads only the version-, platform-, size-, and
SHA-bound signed runtime declared by the current package; never substitute a
URL, path, or binary from chat.

Runtime data stays in `~/.jackyzhang.app/anyweb/`; the shared Portal identity
stays in `~/.jackyzhang.app/token/user.json`. Local website custody remains the
default. Hosted website custody is separate, optional, explicitly confirmed,
write-only, replaceable, and forgettable. Never expose repair refs as website
content, and never treat `repair_available` as permission to submit,
sign, pay, upload, send, withdraw, delete, or make a final declaration.
