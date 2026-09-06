---
name: anydoc
description: >-
  READ THIS FIRST for AnyDoc. Pack this folder, assemble the upload package,
  inspect this client directory, rename and merge these PDFs, build the IRCC
  document folder. Connect / log in / save my portal token for saved models
  and tell Jacky. connect with Jacky / pair session / join code from Jacky.
  Offline inspect and manual plans. One discovery file; playbooks in
  references/. Ask the live CLI (`anydoc commands --json`), never from
  memory of an older release.
when_to_use: |-
  Load on plugin start. Trigger phrases: "pack this folder",
  "assemble the upload package", "inspect this client directory",
  "rename and merge these PDFs", "build the IRCC document folder",
  "connect to anydoc / log in to anydoc / save my portal token",
  "tell Jacky about this", "report this AnyDoc bug / file a bug report",
  "feature request for AnyDoc", "note this as a tip",
  "connect with Jacky / pair session / join code from Jacky".
---

# AnyDoc — host agent contract

Load this on plugin start and whenever the user asks to inspect, plan, assemble,
or verify a client document folder, connect, or Tell Jacky.

AnyDoc organizes the final forms and supporting documents that the user intends
to submit to IRCC or another receiving institution. Its native reader inspects
every supported source first, extracts available page text, and records previews
and protection facts before the host makes semantic decisions. It does not
classify documents, does not run OCR, and never claims a package is ready to
submit.

## Talk to the human

On the first session after install, read [get-started](references/get-started.md)
before the first user-visible reply. Brief the human in plain language — what
AnyDoc does and what to try first — not a command list. After a version bump,
read [whats-new](references/whats-new.md), tell the human what changed in one
breath, then resume their original intent without asking them to repeat the
request. Never ask the human to run doctor as homework. Run the intended ordinary
command first; when stdout is `jz.plugin.envelope.v1`, follow its exact `status`
and `continue_args` until the product reports `ready`, then resume the sealed
request.

Use everyday words. Say what you found and what they must confirm — not flags,
JSON, or file hashes. They decide names, page order, and what to keep or drop.
Never tell the user a package can be filed or that an application is complete.

## When the user asks "what can you do?"

Do not answer from a frozen command list. Run the live client and translate
`commands --json` into product language:

```bash
"$ANYDOC_BIN" commands --json
```

## Agent router — intents

| User intent | Host does | Human may be asked |
|---|---|---|
| "what can AnyDoc do" | Live `"$ANYDOC_BIN" commands --json`; translate inspect/plan/assemble capabilities | Connect when resolving or saving a private model ([connect](references/connect.md)) |
| pack this folder / assemble the upload package / inspect this client directory / rename and merge these PDFs / build the IRCC document folder | [assemble](references/assemble.md): inspect → plan → approve → assemble → verify | Document list or confirmed private model; explicit yes on the packing list; facts for ambiguous subjects |
| connect to anydoc / log in / save my portal token | [connect](references/connect.md): pipe Portal token via stdin | Token file path or one-time paste (never argv) |
| tell Jacky / report bug / feature / tip | [tell-jacky](references/tell-jacky.md) | Confirm the exact draft before send |
| connect with Jacky / pair session / join code from Jacky | [pair-session](references/pair-session.md) | Confirm once that Jacky's assistant may look at this machine's AnyDoc inspect and assemble status |

Playbooks: [connect](references/connect.md), [assemble](references/assemble.md),
[tell-jacky](references/tell-jacky.md), [pair-session](references/pair-session.md).

## Live CLI discovery (fail-closed)

Ask the live CLI, never from memory of an older release:

```bash
"$ANYDOC_BIN" commands --json
```

Command path reference (not for first-session orientation):
[references/command-surface.md](references/command-surface.md).

Resolve the binary once per session, then export `ANYDOC_BIN`:

1. `$ANYDOC_BIN` if already set.
2. Canonical install under `$JACKYZHANG_APP_HOME/plugins/anydoc/current/bin/...`.
3. If missing, run `"$PACKAGE_BIN" doctor --repair-install --json`, then use the canonical path.
4. Do not prefer a random `anydoc` on PATH over the canonical tree.

Runtime data is `~/.jackyzhang.app/anydoc/`. Credentials are
`~/.jackyzhang.app/token/user.json`. Never a second product token file.

## Non-negotiable rules

1. **All packing goes through the AnyDoc CLI.** Do not copy files yourself to “help.”
2. **Approve before assemble.** Unapproved plans are refused.
3. **No plan without a named authority.** **No packing list without a named authority.** A confirmed private model, the user's own document list, or a Public Guide's conventions for deliverables they already listed. Exactly one of these three is enough, and there is no fourth. There is no third `authority.kind`. Filenames and folders are not authority. A folder listing is not a document list.
4. **Encrypted, form, or signed PDFs:** copy or rename only — no split/merge/rotate/compress.
5. **Do not ask the AnyDoc binary to OCR.** Use native text first; read unresolved scans in this primary session only.
6. **Teach Me (only after `model_absent`).** You actually ran `models resolve` in this session and it returned the literal state `model_absent`. Never open Teach Me without that call. A non-zero exit, a timeout, a network or auth error, unparsable output, or any server error is **not** `model_absent`. No other state starts Teach Me. A finished package, snapshot, folder listing, or Public Guide is not a teachable source.
7. **Client intake questionnaires never reach an assembly output, in any
  format.** Form fields, a signature, an official-looking layout, or an official-looking filename never promote intake
  material into a deliverable. Excluded means excluded from every output: never a page inside a merged or combined PDF. Honesty table: Client intake questionnaire (any format).

Full assemble workflow, Teach Me, plan actions, and honesty table:
[assemble](references/assemble.md).
