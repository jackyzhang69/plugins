---
name: anychat
description: >-
  READ THIS FIRST for AnyChat. Local chat archive helper: search my chats,
  chat with X, what did X say in groups, search all chats for keyword,
  search social media chats, cross-platform chat history, contacts,
  identity, export messages with X, export my chat with X, save group
  transcript, download images from group Y, download images from chat with X,
  list voice messages, attachments in group Y, export that PDF,   follow this
  person / group for new messages, save a topic, check a topic, write this
  down, what did we decide, remember this point, connect /
  log in / save my portal token, anychat setup, doctor, tell Jacky, report
  this bug / file a bug report, feature request, note this as a tip.
  One discovery file: playbooks in references/. Ask the live CLI
  (`anychat commands --json`), never from memory of an older release.
when_to_use: |-
  Load on plugin start; reload whenever a user asks anything anychat-related.
  Trigger phrases: "search my chats", "chat with X", "group Y",
  "what did X say in groups", "search all chats for keyword",
  "search social media chats", "cross-platform chat history",
  "contacts", "identity", "export messages with X", "export my chat with X",
  "save group transcript", "download images from group Y",
  "download images from chat with X", "download all images from",
  "list voice messages", "attachments in group Y", "export that PDF",
  "follow this person", "save a topic", "check a topic", "any new messages from",
  "write this down", "what did we decide", "remember this point",
  "connect to anychat / set up anychat", "log in to anychat / save my portal token",
  "anychat setup", "doctor", "tell Jacky", "tell Jacky about this",
  "report this bug / file a bug report", "feature request for AnyChat",
  "note this as a tip".
  Bare "@anychat" / "what can anychat do" / "how do I use anychat":
  if not logged in, follow references/connect.md; otherwise answer from this contract.
---

# AnyChat plugin — agent consumption contract

## Live CLI surface (fail-closed)

Ask the live CLI, never from memory of an older release:

```bash
"$ANYCHAT_BIN" commands --json
```

Volatile catalogs (friend/group lists, live messages) are **not** in that dump.
Playbooks: [connect](references/connect.md), [setup](references/setup.md),
[query](references/query.md), [media](references/media.md),
[export](references/export.md), [tell-jacky](references/tell-jacky.md).

## Advisory marketplace check

On first AnyChat use in each host-agent session, run `"$ANYCHAT_BIN" doctor --check-upgrade --json` before the requested action. If `upgrade.status` is `update_available`, briefly recommend updating, then continue normally. Never auto-update, never block the user, and continue silently when the check is unavailable. Run this once per session load, not before every command.

开通本机档案 uses `"$ANYCHAT_BIN" provision --json` only. Follow [setup](references/setup.md). `needs_agent` means find or request the typed local fact, then supply it over stdin for AnyChat to validate. Do not walk a setup state machine and do not offer Tell Jacky unless that JSON says `blocked` and `offer_tell_jacky` is true. If a marketplace GitHub clone times out, `provision` uses the official ZIP; never `git clone` the plugin repository.

## Advisory inbox check (Tell Jacky replies)

On the first authenticated AnyChat action in each host-agent session, run this as a separate best-effort step before the requested core action:

```bash
"$ANYCHAT_BIN" feedback inbox --json
```

- Show each unread reply to the human in plain language before marking it read.
- After a reply was successfully displayed, run `"$ANYCHAT_BIN" feedback read --update-id <id>` for that reply. Never mark it read first.
- If the inbox request or mark-read fails, warn briefly and continue the requested core action; notification failure never changes the core command's exit result.
- Never inject inbox data into another command's JSON stdout. Inbox/read remain separate commands.
- Run the check once per authenticated host-agent session, not before every command.

**Read this once on plugin load and reload it whenever a user asks anything AnyChat-related.**

AnyChat is a **local chat archive** helper: the user searches/exports **their own**
chat history that already lives on this computer. Content stays on the machine.
Only Portal login + optional redacted “Tell Jacky” feedback leave the device.

## Talk to the human (mandatory — load with this skill)

Governed by platform-vault `delivery/plugin-policy.md` § *Host-agent conversation with the human*.

When speaking to the **person in the chat** (not when writing tool args):

1. **Plain language.** Everyday words. Do not lead with binary paths, `--help` dumps, raw JSON, or internal field names.
2. **What / next, not how.** Say what you are doing for them and what they need to do next—not a play-by-play of every CLI flag.
3. **Major stages only.** Report at phase changes: need connect → 开通本机档案 → ready to search → results ready → need confirm before Tell Jacky. Skip narrating routine tool calls. If 开通 names a supported version and download URL, repeat that wording — do not hide the next action, and do not name a specific social network yourself.
4. **Product language.** “Local chat archive on this computer.” Never expose internal access implementation or component details. A location requested by `needs_agent` is host-agent work: discover and supply it without asking the human for a path. Never name a specific social network in your own words; repeat `say_to_user`.
5. **JSON is for you, not the default chat answer.** Prefer `--format json` / `doctor --json` / `whoami --json` **between tools**; translate outcomes into one or two short human sentences (e.g. “已登录，但还不是管理员” / “还没完成第一次本机设置”).
6. **Mask secrets.** Never paste credentials or private access material into chat.

Host UIs may still show tool cards; **your written reply** must still follow this section.

## 0. Non-negotiable rules

1. **All archive ops go through the bundled `anychat` CLI.** Do not invent local storage paths, access material, or reimplement product internals.
2. **Strict scopes** — scoped queries use exactly one `--mode`; source-qualified/fan-out queries use `--source` or `--all-sources` and never mix legacy scope flags.
3. **Ambiguous names** — run `resolve` first; show typed candidates (person vs group).
4. **No secrets in feedback** — no credentials, private access material, message bodies, friend names/wxids, or attachment bytes unless the user explicitly approves a redacted draft.
5. Prefer `--format json` when chaining agent steps **internally**; follow **Talk to the human** when reporting results. During 开通本机档案, speak only `say_to_user`.

## Self-intro (when user asks “what can you do?”)

Answer in product language, short bullets:

- **Connect** once with a free Portal token ([connect](references/connect.md) / `login`).
- **开通本机档案** on this Mac or Windows PC (`provision`). The agent finds local facts and drives any official install/repair step AnyChat requests; AnyChat validates supplied facts locally. The human only gives consent, opens/quits when unavoidable, or completes 管理员确认.
- **Search** friends, groups, person-across-groups, global keywords, or all supported local sources together.
- **Follow a topic** (a person or a group + what you care about). New messages stay on this computer; the topic itself lives with the account so another computer can follow the same thing.
- **Remember people.** Link someone's accounts once; after that, searching their name covers every platform they are on, without naming accounts again.
- **Export** transcripts, **download** supported attachments, and create a local hash-verifiable evidence bundle.
- **Tell Jacky** feature / bug / tip (always draft → user confirm → `feedback create`).

Stable sources: verified local social-app archives on macOS Apple Silicon and Windows x64. Other apps stay unavailable until their own native tests pass. Automatic first-time access varies by OS and chat-app build. Not a bot; does not send messages.

## Agent quick router

| User intent | Command / playbook |
|-------------|-----------------|
| "what can anychat do / how do I use it" | Answer from this skill (self-intro); if not logged in → [connect](references/connect.md) |
| Connect / token | [connect](references/connect.md) → `login --token-stdin --accept-personal-use` (pipe token; never put secret on argv) |
| Health / which platform | `doctor [--json]` · upgrade: `doctor --check-upgrade` · `status` · `whoami` · `logout` |
| 开通本机档案 | [setup](references/setup.md) → `"$ANYCHAT_BIN" provision --json`. Speak `say_to_user`. If `needs_agent`, satisfy typed `needs` and supply stdin; if `needs_human`, wait, then run `continue_args`. Never old first-run verbs. Never offer Tell Jacky unless `blocked` and `offer_tell_jacky`. |
| **Someone already linked** ("chat with X", "what did X say") | **Check `identity list` first**, then `search --person "X" --all-sources --format json` — one command, every platform they are linked on. Never make them re-link. [query](references/query.md) |
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
| Export transcript | `export --mode … --target … -o ./anychat-export/messages.json` — [export](references/export.md) |
| List attachments | `media list --mode friend --target "…" --type image\|voice\|file\|all` — [media](references/media.md) |
| Download one / all | `media download --id … -o dir` / `media download-all …` |
| Voice → playable | Download voice → **WAV**; **agent runs STT** (anychat has no AI/STT) |
| Tell Jacky | [tell-jacky](references/tell-jacky.md) (confirm draft first) → `feedback create --user-confirmed` |
| Jacky replied / unread replies | `feedback inbox [--json]` → show each reply → `feedback read --update-id <id>` (once per session, best-effort) · `feedback status` · `feedback list` |
| Saved nicknames | `alias set` / `alias list` / `alias rm` · `recents` |
| Local sources | `sources detect` / `sources list` / `sources status` / `sources connect` / `sources sync` / `sources remove` |
| Identity graph | `identity list` / `identity suggest` / `identity link` (draft first, without `--confirm`) / `identity confirm` / `identity unlink` / `identity reset --yes`. Lives with the account. Offline `--person` is no longer available — tell the user to connect first. Auto-suggest is macOS-only; Windows links manually — say which applies |
| Follow a topic | Propose `{人或群} {事}` (about 20 Chinese characters / 40 Latin). Ask 「就叫这个？」. Then `topic save --name "…" --person "…" [--keyword "…"]` or `--conversation source:id`. Never keyword-only. `topic list` / `topic show --topic-id` / `topic check --topic-id` / `topic rm --topic-id --yes`. v1 cannot rename. Need login + at least one ready source. Deleting a topic also deletes its notes. |
| Write down a point | Offer 记下 only when the human asks after a check that had 新增. Draft ≤5 sentences yourself. Never auto-distill. Confirm, then `notes save --topic-id "…" --name "…" --claim "…" --yes` or `notes save --draft '{anychat-notes-v1}' --yes`. `notes list` / `notes show --note-id` / `notes touch --note-id` / `notes rm --note-id --yes`. No local notes file. No excerpts. If this computer has no archive: claims + 「原文不在这台电脑上」. Never claim a one-click jump to one old row. Offline / not logged in: tell them to connect first. |
| Public command list | `commands --json` |

Detail: [query](references/query.md), [media](references/media.md), [export](references/export.md).

## §B. Resolve binary

Prefer the packaged binary next to this skill
(`…/plugins/anychat/current/bin/<platform>/anychat`), not a leftover PATH winner.

1. `$ANYCHAT_BIN` if set, executable, and matching native platform
2. Active plugin runtime (highest priority for agent plugins):
   - Claude plugin root / cache: `…/anychat/<latest-ver>/bin/<platform>/anychat` or `$CLAUDE_PLUGIN_ROOT/bin/<platform>/anychat[.exe]`
   - Canonical current: `~/.jackyzhang.app/plugins/anychat/current/bin/<platform>/anychat`
3. Canonical standalone installation:
   - macOS: `$HOME/.local/bin/anychat` · Windows: `%USERPROFILE%\.local\bin\anychat.exe` (must sit next to `anychat-access`; verify not an older version than plugin manifest)
4. `command -v anychat` / `where anychat` (last resort fallback)

Export `ANYCHAT_BIN` once per session. On Windows use `anychat.exe`.

## Mode cheat-sheet

| Mode | Meaning |
|------|---------|
| `friend` | 1:1 only |
| `group` | one group, all senders |
| `person-in-groups` | one person, groups only |
| `person-in-group` | one person + one group |
| `me-in-groups` / `me-in-group` | self in groups |
| `multi-in-group` | multiple people in one group |

## Execution boundaries

| Work | Where |
|------|--------|
| Unlock / query / export / media copy | **Local CLI** (user machine) |
| Login entitlement / Tell Jacky submit | **Portal** `account.jackyzhang.app` (accountd) |
| Chat message bodies | **Never uploaded** |

## After success (stickiness, light touch)

- Offer at most **one** soft “try next” suggestion.
- During 开通本机档案, do not offer Tell Jacky unless `provision` says `blocked` and `offer_tell_jacky`. After the archive is ready, offer it once only if they ask or the same non-setup failure repeats.
- Do not spam feedback or re-login prompts.

## Product language

Describe as a **local chat archive** helper. Do not discuss internal access implementation with the user. See **Talk to the human** above.
