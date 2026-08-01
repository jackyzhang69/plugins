---
name: anychat-query
description: >-
  Query local chat archive: friend 1:1, group, person-in-groups, global search,
  friends/groups list, resolve, contact card. Requires connect + setup.
when_to_use: |-
  "chat with X", "group Y", "what did X say in groups", "search all chats for keyword".
---

# AnyChat query

## Advisory marketplace check

On first AnyChat use in each host-agent session, run `"$ANYCHAT_BIN" doctor --check-upgrade --json` before the requested action. If `upgrade.status` is `update_available`, briefly recommend updating from the marketplace, then continue normally. Never auto-update, never block the user, and continue silently when the check is unavailable. Run this once per session load, not before every command.

Resolve `$ANYCHAT_BIN` via **anychat-capabilities** §B.

## Before query

```bash
"$ANYCHAT_BIN" doctor --json
```

If not setup → **anychat-setup**. If not logged in → **connect-anychat**.

## Disambiguate first when name is fuzzy

```bash
"$ANYCHAT_BIN" resolve --query "<name>" --json
```

Pick **one** candidate with correct `kind` (person vs group). Never collapse types.

## Common commands

```bash
# Friend 1:1
"$ANYCHAT_BIN" query --mode friend --target "<name-or-id>" --days 30 --limit 50 --format text

# One group
"$ANYCHAT_BIN" query --mode group --target "<group>" --days 30 --limit 50

# Person across groups
"$ANYCHAT_BIN" query --mode person-in-groups --target "<person>" --days 30 --limit 50

# Person in one group
"$ANYCHAT_BIN" query --mode person-in-group --target "<person>" --group "<group>" --days 30

# Multi senders in one group (repeat --person)
"$ANYCHAT_BIN" query --mode multi-in-group --target "<A>" --person "<B>" --group "<group>" --days 30

# Keyword on a mode + ±N context lines (F5)
"$ANYCHAT_BIN" query --mode group --target "<group>" --keyword "<word>" --context 2 --days 30

# Global keyword
"$ANYCHAT_BIN" search --keyword "<word>" --days 90 --limit 50 --format json

# Discovery
"$ANYCHAT_BIN" friends list --query "<q>" --json
"$ANYCHAT_BIN" groups list --query "<q>" --limit 50 --json
"$ANYCHAT_BIN" groups members --query "<group>" --limit 100 --json
"$ANYCHAT_BIN" contacts card --query "<q>" --json
"$ANYCHAT_BIN" sessions --limit 30 --json

# Export transcript to file
"$ANYCHAT_BIN" export --mode friend --target "<name>" --days 30 -o ./anychat-export/messages.json
```

Prefer `--format json` for chaining; present human summary to the user.

## Attachments / voice

If user wants images/files/voice → **anychat-media**.  
Voice download is **playable WAV**; **transcript/text is agent STT** (anychat has no AI).

```bash
"$ANYCHAT_BIN" media download --id "<id>" -o ./anychat-export --json
# use playable_path from JSON with your own speech-to-text
```
