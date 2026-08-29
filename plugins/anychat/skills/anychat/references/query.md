# AnyChat query

## Advisory marketplace check

On first AnyChat use in each host-agent session, run `"$ANYCHAT_BIN" doctor --check-upgrade --json` before the requested action. If `upgrade.status` is `update_available`, briefly recommend updating, then continue normally. Never auto-update, never block the user, and continue silently when the check is unavailable. Run this once per session load, not before every command. If the host marketplace GitHub clone times out, download `https://github.com/jackyzhang69/plugins/archive/refs/heads/main.zip` yourself. Never `git clone` that repository as the update path.

Resolve `$ANYCHAT_BIN` via the product router §B.

## Before query

```bash
"$ANYCHAT_BIN" doctor --json
```

If not setup → [setup](setup.md). If not logged in → **connect**.

## Someone the user already linked — try this first

When the user names a **person** ("chat with Minko", "what did Minko say"),
check the identity graph **before** asking them which platform or which account:

```bash
"$ANYCHAT_BIN" identity list --json
```

If that name is already linked, one command answers across every platform they
confirmed. Do not make them link again, and do not ask which app to search:

```bash
"$ANYCHAT_BIN" search --person "<name>" --all-sources --days 90 --format json
```

- `--person` accepts the name the user linked. `--keyword` is optional here;
  add it to narrow within that person's messages.
- Only the platforms with a confirmed account for that person are searched.
  Others appear in `skipped_sources` with a reason — report that honestly
  rather than implying the person had nothing to say there.
- Results carry each message's platform and original conversation. When you
  report back, say the person's name and which platforms answered.

If the name is **not** linked yet, fall back to the per-platform commands
below. Offer to link only when it would actually help — the same person turning
up in two places, or one name with several possible accounts. `identity suggest`
already applies that test, so prefer it over inventing your own prompt.

## Multi-Source Fan-out & Single-Source Routing

AnyChat supports unified querying across local chat sources:
- **`--all-sources`**: Search across all registered and ready local social-app archives with timestamp ordering.
- **`--source <source-id>`**: Scope the query strictly to one registered platform. Use the source id from `sources list --json` / `commands --json`.

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

# Scoped query modes
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

Links one person's accounts across platforms so a later search can name them
once. The graph lives with the account. Chat bodies stay on this computer.
`--person` and `topic` fail closed when offline: tell the user to connect
first. Keyword-only search still works offline.

```bash
# Who is linked, which platforms, and who has never been searched for
"$ANYCHAT_BIN" identity list [--json]

# Only what needs a human decision (macOS: reads local Contacts)
"$ANYCHAT_BIN" identity suggest [--json]

# Preview the change — writes nothing
"$ANYCHAT_BIN" identity link --person-label "<Name>" --source <source-id> --raw-id "<id>"

# Apply a new link
"$ANYCHAT_BIN" identity link --person-label "<Name>" --source <source> --raw-id "<id>" --confirm

# Confirm an alias `identity list` already shows as not confirmed
"$ANYCHAT_BIN" identity confirm --source <source> --raw-id "<id>"

# Stop including an account (effective on the next search; nothing to rebuild)
"$ANYCHAT_BIN" identity unlink --source <source> --raw-id "<id>"

# Remove everything
"$ANYCHAT_BIN" identity reset --yes
```

Rules for the agent:

- **Always show the draft before confirming.** Run `link` without `--confirm`,
  show the user what would change in plain language, and only then re-run with
  `--confirm`. `suggest` and a `link` preview write nothing, so they cannot be
  finished with `identity confirm`. Use `identity confirm` only when
  `identity list` already shows the account as not confirmed. Never link silently.
- **Report in product language.** After a confirm, say the person's name and
  the platforms now covered. Never read raw ids or internal person ids aloud.
- **macOS and Windows differ, and you must say which one applies.** Automatic
  suggestions read the Mac's Contacts and do not exist on Windows. On Windows
  linking is manual, and works identically once linked — describe it as a
  different first step, not as something broken or missing.
- Searches carry an opaque token per linked account, never the email, phone
  number, or wxid itself.

## Follow a topic (`anychat topic`)

A topic is `{人 or 群} + {事}`. The host proposes a name (about 20 Chinese
characters / 40 Latin), asks 「就叫这个？」, then saves with `--name`.
v1 cannot rename. Keyword-only is rejected — a global keyword search cannot
cover a time window.

```bash
# Propose, then save after the human agrees
"$ANYCHAT_BIN" topic save --name "<人 事>" --person "<name>" [--keyword "<word>"]
"$ANYCHAT_BIN" topic save --name "<群 事>" --conversation <source>:<id>

"$ANYCHAT_BIN" topic list [--json]
"$ANYCHAT_BIN" topic show --topic-id "<uuid>" [--json]
"$ANYCHAT_BIN" topic check --topic-id "<uuid>"
"$ANYCHAT_BIN" topic rm --topic-id "<uuid>" --yes
```

`topic show` / `check` never print chat bodies. New-message counts only.
Needs login and at least one ready local source. Offline `--person` is gone.
Deleting a topic also deletes notes for that topic.

## Write down a point (`anychat notes`)

Host drafts. Binary does not call models. Offer 记下 only when the human
asks after a check that had 新增. At most 5 new claims. Confirm first.

```bash
"$ANYCHAT_BIN" notes save --topic-id "<uuid>" --name "<群或人 事>" --claim "<sentence>" --yes
"$ANYCHAT_BIN" notes save --draft '{"contract":"anychat-notes-v1",...}' --yes
"$ANYCHAT_BIN" notes list [--json]
"$ANYCHAT_BIN" notes show --note-id "<uuid>" [--json]
"$ANYCHAT_BIN" notes touch --note-id "<uuid>"
"$ANYCHAT_BIN" notes rm --note-id "<uuid>" --yes
```

No chat text, no excerpts, no get-by-raw-id. If this device has no
archive: show the claims and 「原文不在这台电脑上」. To re-read, search
the pinned conversation around `at`. Need login. Offline fails closed.

## Disambiguate name & Discovery

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
