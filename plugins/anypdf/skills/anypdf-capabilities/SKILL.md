---
name: anypdf-capabilities
description: >-
  READ THIS FIRST for AnyPDF. Router for "what can AnyPDF do / which forms /
  how do I use it". Answer from the live CLI, never from memory of an older
  release or a frozen form list.
---

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
- If the user is not connected, run **connect-anypdf** first, then catalog.

## Agent router

| User intent | Skill / command |
|---|---|
| "what can AnyPDF do / which forms" | Live `$ANYPDF forms catalog --json` + `$ANYPDF doctor --json` |
| Connect / login | **connect-anypdf** |
| Fill a registered PDF | **anypdf-fill** (resolve via `$ANYPDF forms resolve`) |
| New official blank form | **anypdf-form-intake** |
| Feature / bug / tip for Jacky | **tell-jacky** |

After a marketplace install or update, **connect-anypdf** repairs from this
package into `~/.jackyzhang.app/plugins/anypdf/current`. Later agent commands
use `$ANYPDF` at that `current` binary, not whichever `anypdf` is first on PATH.

## Recovery before escalation

Keep typed recovery details between tools and explain the next choice in plain
language. Follow the bounded recovery procedure in **anypdf-fill** before
suggesting **tell-jacky**. Never send feedback automatically.
