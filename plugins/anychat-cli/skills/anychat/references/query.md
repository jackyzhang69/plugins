# AnyChat query

## Advisory marketplace check

On first AnyChat use in each host-agent session, run `"$ANYCHAT_BIN" doctor --check-upgrade --json` before the requested action. If `upgrade.status` is `update_available`, briefly recommend updating from the marketplace, then continue normally. Never auto-update, never block the user, and continue silently when the check is unavailable. Run this once per session load, not before every command.

Resolve `$ANYCHAT_BIN` via the product router §B.

## Before query

```bash
"$ANYCHAT_BIN" doctor --json
```

If not setup → [setup](setup.md). If not logged in → **connect**.

## Multi-Source Fan-out & Single-Source Routing

AnyChat supports unified querying across local chat sources:
- **`--all-sources`**: Search across all registered and ready platforms (WeChat, Telegram, iMessage) with timestamp ordering.
- **`--source <source-id>`**: Scope the query strictly to one registered platform (`wechat`, `telegram`, `imessage`).

### 1. Cross-Platform Unified Search & Query

```bash
# Search across all local chat sources by keyword
"$ANYCHAT_BIN" search --keyword "<word>" --all-sources --days 90 --limit 50 --format json

# Unified timeline query across all sources
"$ANYCHAT_BIN" query --all-sources --days 30 --limit 50 --format json
```

### 2. Platform-Specific Queries

```bash
# iMessage: Single source query (specify --account when multiple accounts exist)
"$ANYCHAT_BIN" query --source imessage --account "<account-token>" --days 90 --limit 50 --format json

# Telegram: Local Postbox cache query
"$ANYCHAT_BIN" query --source telegram --days 30 --limit 50 --format json

# WeChat: Legacy scoped query modes
"$ANYCHAT_BIN" query --mode friend --target "<name-or-id>" --days 30 --limit 50 --format text
"$ANYCHAT_BIN" query --mode group --target "<group>" --days 30 --limit 50
"$ANYCHAT_BIN" query --mode person-in-groups --target "<person>" --days 30 --limit 50
"$ANYCHAT_BIN" query --mode person-in-group --target "<person>" --group "<group>" --days 30
"$ANYCHAT_BIN" query --mode multi-in-group --target "<A>" --person "<B>" --group "<group>" --days 30
"$ANYCHAT_BIN" query --mode group --target "<group>" --keyword "<word>" --context 2 --days 30
"$ANYCHAT_BIN" search --keyword "<word>" --days 90 --limit 50 --format json
```

## iMessage Account Disambiguation

When querying iMessage on a machine with multiple Apple IDs / accounts:
1. Run `"$ANYCHAT_BIN" sources detect --json` to list accounts with their `display_hint` (e.g. `"196 messages · 2023-05 → 2026-02"`).
2. Choose the account token matching the desired history and pass `--account <token>`.

## Local Identity Graph (`anychat identity`)

Map and manage unified person identities across platforms:

```bash
# List all mapped local persons and their aliases
"$ANYCHAT_BIN" identity list [--json]

# Discover alias suggestions from macOS Contacts
"$ANYCHAT_BIN" identity suggest [--json]

# Link an alias to a person
"$ANYCHAT_BIN" identity link --person-label "<Name>" --source <imessage|wechat|telegram> --raw-id "<id>"

# Explicitly confirm a suggestion
"$ANYCHAT_BIN" identity confirm --source <source> --raw-id "<id>"

# Unlink an alias
"$ANYCHAT_BIN" identity unlink --source <source> --raw-id "<id>"
```

## Disambiguate name & Discovery (WeChat)

```bash
"$ANYCHAT_BIN" resolve --query "<name>" --json
"$ANYCHAT_BIN" friends list --query "<q>" --json
"$ANYCHAT_BIN" groups list --query "<q>" --limit 50 --json
"$ANYCHAT_BIN" groups members --query "<group>" --limit 100 --json
"$ANYCHAT_BIN" contacts card --query "<q>" --json
"$ANYCHAT_BIN" sessions --limit 30 --json
```

## Export transcript to file

```bash
"$ANYCHAT_BIN" export --mode friend --target "<name>" --days 30 -o ./anychat-export/messages.json
```

## Attachments & Voice

If user wants images/files/voice → [media](media.md).  
Voice download is **playable WAV**; **transcript/text is agent STT** (anychat has no AI).

```bash
"$ANYCHAT_BIN" media download --id "<id>" -o ./anychat-export --json
# use playable_path from JSON with your own speech-to-text
```

## Verifiable Evidence Bundle

```bash
# Create verifiable local evidence bundle across sources
"$ANYCHAT_BIN" evidence create --all-sources --output ./anychat-evidence
"$ANYCHAT_BIN" evidence verify --bundle ./anychat-evidence
```
