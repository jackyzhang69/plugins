---
name: anyweb
description: >-
  READ THIS FIRST for AnyWeb. Fill a supported Express Entry profile in
  visible Chrome on this computer, stopping before submit. Ask only for
  real facts the site still needs. Save or forget a website login for
  reuse here. Check whether a website repair claim is queued or ready.
  Connect or log in with the shared Portal token, check identity and
  doctor, and Tell Jacky with a feature request, bug report, or knowledge
  tip. Connect with Jacky, start a pair session, or use a join code so
  Jacky's assistant can look at this machine's AnyWeb status.
  This is not a general web automation tool.
when_to_use: |-
  Load on plugin start. Trigger phrases: "create or continue my Express
  Entry profile", "fill my EE profile up to the final review step",
  "continue this supported website draft on my computer",
  "check or resume my AnyWeb task", "check my AnyWeb repair",
  "is the website repair ready", "connect or log in to AnyWeb",
  "save my Portal token for AnyWeb", "Tell Jacky about AnyWeb",
  "save my website login", "forget my website login",
  "answer this saved security question",
  "report an AnyWeb bug", "request an AnyWeb feature",
  "send an AnyWeb knowledge tip",
  "connect with Jacky", "pair session", "join code from Jacky".
---

# AnyWeb — host agent contract

Load this on plugin start and whenever the user asks about website repair,
saved login, supported local tasks, connect, pair session, or Tell Jacky.

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
| "what can AnyWeb do" | Live `"$ANYWEB_BIN" commands --json`; translate to supported repair, task, and saved-login capabilities | Connect once if not logged in ([connect](references/connect.md)) |
| check my AnyWeb repair / is the website repair ready | [repair](references/repair.md): `repair claims --json`; translate queue status | Facts to reproduce a blocked repair when the product asks |
| connect or log in to AnyWeb / save my Portal token | [connect](references/connect.md): pipe Portal token via stdin | Token file path or one-time paste (never argv) |
| save my website login / forget my website login / answer this saved security question | [saved login](references/hosted-continuity.md) | Confirm before storing or replacing a saved login; security answers only when the product requests them |
| continue this supported website draft / create or continue my Express Entry profile / check or resume my AnyWeb task | [local tasks](references/tasks.md): bounded visible Chrome on this computer | Facts the site needs; confirm before sensitive steps; verify outcome matches expectation |
| Tell Jacky about AnyWeb / report an AnyWeb bug / request an AnyWeb feature / send an AnyWeb knowledge tip | [Tell Jacky](references/tell-jacky.md) | Confirm the exact draft before send |
| connect with Jacky / pair session / join code from Jacky | [pair-session](references/pair-session.md) | Confirm once that Jacky's assistant may look at this machine's AnyWeb repair, task, or saved-login status |

Playbooks: [connect](references/connect.md), [saved login](references/hosted-continuity.md),
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
stays in `~/.jackyzhang.app/token/user.json`. Website work stays in visible
Chrome on this computer. A saved login is only for reuse on this computer and
never exposes the stored password or answer in chat. Never expose repair refs
as website content, and never treat `repair_available` as permission to submit,
sign, pay, upload, send, withdraw, delete, or make a final declaration.
