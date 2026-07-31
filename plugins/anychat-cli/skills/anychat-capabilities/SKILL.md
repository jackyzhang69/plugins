---
name: anychat-capabilities
description: >-
  READ THIS FIRST for AnyChat. Intent → CLI router for local chat archive
  queries, media download, setup, and Tell Jacky. Load every AnyChat session.
when_to_use: |-
  Any anychat / local chat archive / "search my wechat-like chats" intent after connect.
---

# AnyChat — agent consumption contract

**All archive operations go through the `anychat` CLI.** Do not reimplement parsing. Do not invent database paths or keys.

## §B. Resolve binary

1. `$ANYCHAT_BIN` if set and executable  
2. `$HOME/.local/bin/anychat`  
3. Plugin cache: `…/anychat-cli/<ver>/bin/darwin-arm64/anychat`  
4. `command -v anychat`

Export `ANYCHAT_BIN` once per session.

## Non-negotiable rules

1. **Strict modes** — every query has exactly one `--mode`. Friend and group never mix.  
2. **Ambiguous names** — run `resolve` first; show typed candidates (person vs group).  
3. **No secrets in feedback** — never send tokens, message bodies, or attachment bytes without explicit redacted draft + user confirm.  
4. **Content stays local** — only Portal auth/feedback leave the machine.  
5. Prefer `--format json` when chaining agent steps.

## Intent router

| User intent | Command |
|-------------|---------|
| Connect / token | `login --token … --accept-personal-use` → **connect-anychat** |
| Health | `doctor [--json]` · upgrade: `doctor --check-upgrade` |
| First-time local access | **anychat-setup** → `setup` |
| Chat with friend only | `query --mode friend --target "…" --days 30` |
| One group | `query --mode group --target "…" --days 30` |
| Person across all groups | `query --mode person-in-groups --target "…"` |
| Person in one group | `query --mode person-in-group --target "…" --group "…"` |
| Multi people in one group | `query --mode multi-in-group --target "A" --person "B" --group "…"` |
| My messages in groups | `query --mode me-in-groups` / `me-in-group --group "…"` |
| Global keyword | `search --keyword "…" --days 90` |
| Keyword + context | `query … --keyword "…" --context 2` |
| List friends / groups | `friends list` / `groups list [--limit N]` |
| Group member roster | `groups members --query "…" --json` |
| Disambiguate name | `resolve --query "…" --json` |
| Contact card | `contacts card --query "…"` |
| Export transcript | `export --mode … --target … -o ./anychat-export/messages.json` |
| List attachments | `media list --mode friend --target "…" --type image\|voice\|file\|all` |
| Download one / all | `media download --id … -o dir` / `media download-all …` |
| Voice playable + text | Download voice → **WAV**; **agent runs STT** (anychat has no AI/STT) |
| Tell Jacky | **tell-jacky** skill (confirm draft first) |
| Saved nicknames | `alias set/list/rm` · `recents` |

## Mode cheat-sheet

| Mode | Meaning |
|------|---------|
| `friend` | 1:1 only |
| `group` | one group, all senders |
| `person-in-groups` | one person, groups only |
| `person-in-group` | one person + one group |
| `me-in-groups` / `me-in-group` | self in groups |
| `multi-in-group` | multiple people in one group |

## After success (stickiness, light touch)

- Offer at most **one** soft “try next” suggestion.  
- On repeated failures, offer **tell-jacky** once (opt-in).  
- Do not spam feedback or re-login prompts.

## Product language

Describe the product as a **local chat archive** helper. Do not discuss encryption, keys, or extraction methods with the user.
