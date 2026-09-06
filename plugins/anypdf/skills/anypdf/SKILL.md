---
name: anypdf
description: >-
  READ THIS FIRST for AnyPDF. Fill a registered PDF form, submit a PDF
  template as a new form request, connect / login --token-stdin, tell Jacky.
  Answer from the live CLI, never from memory of an older release or a frozen form list.
  One discovery file; playbooks in references/. Ask
  `anypdf commands --json`.
when_to_use: |-
  Load on plugin start. "what can AnyPDF do", "which forms", "fill a registered form",
  "fill a registered PDF", "submit a PDF template", "connect / log in",
  "tell Jacky".
---

# AnyPDF — what this plugin can do

Load this on plugin start, and again when the user asks what AnyPDF can do,
which forms it fills, or how to use it.

## Talk to the human

Speak in everyday words. Say what you can do and what happens next. Do not
lead with binary paths, `--help`, or raw JSON. Keep CLI and JSON between
tools. Never show credentials. On the first session after install, read
[references/get-started.md](references/get-started.md) before your first
user-visible reply. After a version update, read
[references/whats-new.md](references/whats-new.md) and resume the prior intent
without asking the human to repeat it. Never ask the human to run doctor as
homework. Run the intended ordinary command first; when stdout is
`jz.plugin.envelope.v1`, follow its exact `status` and `continue_args` until
the product reports `ready`, then resume the sealed request.

## When the user asks "what can you do?"

Use this as a runtime answer harness. The answer is valid only after live
discovery; do not fill it from this file's memory, a previous chat, or a
handwritten form list.

1. If the client is not connected, follow [connect](references/connect.md),
   then run the live client:

```bash
$ANYPDF doctor --json
$ANYPDF forms catalog --json
```

2. Treat the returned values as the current facts. `doctor` establishes that
   this installed client is ready; `forms catalog` supplies the current
   published entries that may be offered for filling. Do not add an entry,
   count, category, example, or promise that is not supported by those values.

3. Organize a short product answer from the returned entries only:

   - Describe published entries as available for filling only when the live
     catalog returned them. Keep any names or identifiers tied to those
     returned records.
   - Treat an issuing-authority official blank PDF that is not in the catalog
     as recognition or support-request input only. It is not immediately
     fillable, and the answer must not promise that it will become fillable
     right away.
   - Explain that a fill uses the user's confirmed information. Ask only for
     a missing fact or a decision that the current request actually needs;
     otherwise do not ask for extra information.
   - Say that AnyPDF does not guess missing facts and does not submit an
     application or other filing to an issuing authority on the user's behalf.

4. Translate the result into everyday product language. Keep CLI names, JSON,
   paths, credentials, raw errors, support/debug fields, and implementation
   details between tools; none of them belongs in the human-facing answer. If
   live discovery cannot verify availability, say so without inventing a
   capability.

## Agent router — intents

| User intent | Host does | Human may be asked |
|---|---|---|
| "what can AnyPDF do / which forms" | Live `$ANYPDF forms catalog --json` + `$ANYPDF doctor --json`; translate to product language | Connect once if not logged in ([connect](references/connect.md)) |
| Connect / login | [connect](references/connect.md): pipe Portal token via stdin | Token file path or one-time paste (never argv) |
| Fill a registered PDF | [fill](references/fill.md): resolve form, gather facts, submit fill job | Facts for fields; confirm before irreversible steps |
| New official blank form | [intake](references/intake.md): one PDF upload | Confirm the exact blank PDF is the issuing-authority official form |
| Feature / bug / tip for Jacky | [tell-jacky](references/tell-jacky.md) | Confirm the exact draft before send |

Playbooks: [connect](references/connect.md), [fill](references/fill.md),
[intake](references/intake.md), [tell-jacky](references/tell-jacky.md).

## Live CLI discovery (fail-closed)

Ask the live CLI, never from memory of an older release. Volatile form catalogs
are not dumped here.

```bash
"$ANYPDF" commands --json
```

Command path reference (not for first-session orientation):
[references/command-surface.md](references/command-surface.md).

After a marketplace install or update, [connect](references/connect.md) repairs from this
package into `~/.jackyzhang.app/plugins/anypdf/current`. Later agent commands
use `$ANYPDF` at that `current` binary, not whichever `anypdf` is first on PATH.

## Recovery before escalation

Keep typed recovery details between tools and explain the next choice in plain
language. Follow the bounded recovery procedure in [fill](references/fill.md) before
suggesting [tell-jacky](references/tell-jacky.md). Never send feedback automatically.
