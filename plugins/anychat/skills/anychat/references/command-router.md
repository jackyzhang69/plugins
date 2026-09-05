# Command router and CLI paths (coverage reference)

Not for first-session orientation. Host agents discover the live surface via
`"$ANYCHAT_BIN" commands --json`. Use this table after intent routing — never
as the first user-visible answer.

## Agent quick router

| User intent | Command / playbook |
|-------------|-----------------|
| "what can anychat do / how do I use it" | Live `commands --json` translated; if not logged in → [connect](connect.md) |
| Connect / token | [connect](connect.md) → `login --token-stdin --accept-personal-use` (pipe token; never put secret on argv) |
| Health / which platform | `doctor [--json]` · check: `doctor --check-upgrade` · explicit update: `update --json` · `status` · `whoami` · `logout` |
| 开通本机档案 | [setup](setup.md) → `"$ANYCHAT_BIN" provision --json`. Speak `say_to_user`. If `needs_agent`, satisfy typed `needs` and supply stdin; if `needs_human`, wait, then run `continue_args`. Never old first-run verbs. Never offer Tell Jacky unless `blocked` and `offer_tell_jacky`. |
| **Someone already linked** ("chat with X", "what did X say") | **Check `identity list` first**, then `search --person "X" --all-sources --format json` — one command, every platform they are linked on. Never make them re-link. [query](query.md) |
| Chat with friend only | `query --mode friend --target "…" --days 30` |
| One group | `query --mode group --target "…" --days 30` |
| Person across all groups | `query --mode person-in-groups --target "…"` |
| Person in one group | `query --mode person-in-group --target "…" --group "…"` |
| Multi people in one group | `query --mode multi-in-group --target "A" --person "B" --group "…"` |
| My messages in groups | `query --mode me-in-groups` / `me-in-group --group "…"` |
| Global keyword | `search --keyword "…" --days 90` |
| Search all stable local sources | `search --keyword "…" --all-sources --format json` |
| Create / verify local evidence | `evidence create --all-sources --output <new-dir>` / `evidence verify --bundle <dir>` |
| Keyword + context | `query … --keyword "…" --context 2` |
| List friends / groups | `friends list` / `groups list [--limit N]` |
| Group member roster | `groups members --query "…" --json` |
| Disambiguate name | `resolve --query "…" --json` |
| Contact card | `contacts card --query "…"` |
| Recent sessions | `sessions` |
| Export transcript | `export --mode … --target … -o ./anychat-export/messages.json` — [export](export.md) |
| List attachments | `media list --mode friend --target "…" --type image\|voice\|file\|all` — [media](media.md) |
| Download one / all | `media download --id … -o dir` / `media download-all …` |
| Voice → playable | Download voice → **WAV**; **agent runs STT** (anychat has no AI/STT) |
| Tell Jacky | [tell-jacky](tell-jacky.md) → `feedback preview`, confirm exact draft, then `feedback create --user-confirmed --confirmation-binding <binding>` |
| Jacky replied / unread replies | `feedback inbox [--json]` → show each reply → `feedback read --update-id <id>` (once per session, best-effort) · `feedback status` · `feedback list` |
| Saved nicknames | `alias set` / `alias list` / `alias rm` · `recents` |
| Local sources | `sources detect` / `sources list` / `sources status` / `sources connect` / `sources sync` / `sources remove` |
| Identity graph | `identity list` / `identity suggest` / `identity link` (draft first, without `--confirm`) / `identity confirm` / `identity unlink` / `identity reset --yes`. Lives with the account. Offline `--person` is no longer available — tell the user to connect first. Auto-suggest is macOS-only; Windows links manually — say which applies |
| Follow a topic | Propose `{人或群} {事}` (about 20 Chinese characters / 40 Latin). Ask 「就叫这个？」. Then `topic save --name "…" --person "…" [--keyword "…"]` or `--conversation source:id`. Never keyword-only. `topic list` / `topic show --topic-id` / `topic check --topic-id` / `topic rm --topic-id --yes`. v1 cannot rename. Need login + at least one ready source. Deleting a topic also deletes its notes. |
| Write down a point | Offer 记下 only when the human asks after a check that had 新增. Draft ≤5 sentences yourself. Never auto-distill. Confirm, then `notes save --topic-id "…" --name "…" --claim "…" --yes` or `notes save --draft '{anychat-notes-v1}' --yes`. `notes list` / `notes show --note-id` / `notes touch --note-id` / `notes rm --note-id --yes`. No local notes file. No excerpts. If this computer has no archive: claims + 「原文不在这台电脑上」. Never claim a one-click jump to one old row. Offline / not logged in: tell them to connect first. |
| Public command list | `commands --json` |

## Mode cheat-sheet

| Mode | Meaning |
|------|---------|
| `friend` | 1:1 only |
| `group` | one group, all senders |
| `person-in-groups` | one person, groups only |
| `person-in-group` | one person + one group |
| `me-in-groups` / `me-in-group` | self in groups |
| `multi-in-group` | multiple people in one group |

## Advisory session checks (host-only)

On first AnyChat use per session: `"$ANYCHAT_BIN" doctor --check-upgrade --json` — if
`update_available`, recommend updating briefly, then continue. Never auto-update or
block. Explicit human update request: `"$ANYCHAT_BIN" update --json`.

On first authenticated action per session: `"$ANYCHAT_BIN" feedback inbox --json` —
show unread replies, then `feedback read --update-id <id>` after display. Best-effort;
never block the core action.

Provision path: `"$ANYCHAT_BIN" provision --json` for 开通本机档案. Follow
[setup](setup.md).

Detail: [query](query.md), [media](media.md), [export](export.md).
