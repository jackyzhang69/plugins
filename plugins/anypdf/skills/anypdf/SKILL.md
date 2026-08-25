---
name: anypdf
description: >-
  READ THIS FIRST for AnyPDF. Fill a registered PDF form, submit a PDF
  template as a new form request, connect / login --token-stdin, tell Jacky.
  Answer from the live CLI, never from memory of an older release or a frozen form list.
  One discovery file; playbooks in references/. Ask
  `anypdf commands --json`.
when_to_use: |-
  Load on plugin start. "what can AnyPDF do", "which forms", "fill IMM5257",
  "fill a registered PDF", "submit a PDF template", "connect / log in",
  "tell Jacky".
---

# AnyPDF — what this plugin can do

## Live CLI surface (fail-closed)

Ask the live CLI, never from memory of an older release. Volatile form catalogs
are not dumped here.

```bash
"$ANYPDF" commands --json
```

Playbooks: [connect](references/connect.md), [fill](references/fill.md),
[intake](references/intake.md), [tell-jacky](references/tell-jacky.md).

Public commands: `login`, `whoami`, `logout`, `doctor`, `read`, `validate`,
`forms catalog`, `forms resolve`, `forms schema`, `fill submit`,
`fill readiness`, `fill status`, `fill download`, `intake submit`,
`feedback submit`, `feedback status`, `knowledge list`, `knowledge add`,
`knowledge remove`, `commands`.

# AnyPDF — what this plugin can do

Load this on plugin start, and again when the user asks what AnyPDF can do,
which forms it fills, or how to use it.

## Talk to the human

Speak in everyday words. Say what you can do and what happens next. Do not
lead with binary paths, `--help`, or raw JSON. Keep CLI and JSON between
tools. Never show credentials.

AnyPDF fills official PDF forms the **live catalog** already knows, accepts
one official blank PDF when a form is missing, and can send Jacky a note. It
does not invent facts, does not submit to IRCC, and does not fill a form that
is not in the live catalog.

## When the user asks "what can you do?"

Do not answer from this file's memory, a previous chat, or a handwritten form
list. After connect, run the live client and translate the JSON into a short
product answer:

```bash
$ANYPDF doctor --json
$ANYPDF forms catalog --json
```

- `doctor` is this machine: version, login, install home.
- `forms catalog` is the **server's current published forms**. That list is
  the capability. If a form is not in that response, it is not available in
  this release.
- If the user is not connected, run [connect](references/connect.md) first, then catalog.

## Agent router

| User intent | Skill / command |
|---|---|
| "what can AnyPDF do / which forms" | Live `$ANYPDF forms catalog --json` + `$ANYPDF doctor --json` |
| Connect / login | [connect](references/connect.md) |
| Fill a registered PDF | [fill](references/fill.md) (resolve via `$ANYPDF forms resolve`) |
| New official blank form | [intake](references/intake.md) |
| Feature / bug / tip for Jacky | [tell-jacky](references/tell-jacky.md) |

After a marketplace install or update, [connect](references/connect.md) repairs from this
package into `~/.jackyzhang.app/plugins/anypdf/current`. Later agent commands
use `$ANYPDF` at that `current` binary, not whichever `anypdf` is first on PATH.

## Recovery before escalation

Keep typed recovery details between tools and explain the next choice in plain
language. Follow the bounded recovery procedure in [fill](references/fill.md) before
suggesting [tell-jacky](references/tell-jacky.md). Never send feedback automatically.
