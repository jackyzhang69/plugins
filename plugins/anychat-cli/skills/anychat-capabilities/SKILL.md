---
name: anychat-capabilities
description: >-
  READ THIS FIRST for AnyChat. One-page consumption contract for AI agents:
  what AnyChat can do, how to resolve the binary, which skill/command to call
  for each user intent, and how to answer "what can you do / how do I use
  anychat" without guessing. Load on plugin start and whenever the user asks
  anything AnyChat-related.
when_to_use: |-
  Load on plugin start; reload whenever a user asks anything anychat-related.
  Trigger phrases: "search my chats", "export messages with X", "download images
  from group Y", "anychat setup", "doctor", "tell Jacky".
  Also: a bare/ambiguous first mention of "anychat" with no other task —
  "@anychat", "anychat", "hi anychat", "what can anychat do", "how do I use
  anychat" — if not logged in, route to connect-anychat first; otherwise answer
  from this contract (self-intro) directly.
---

# AnyChat plugin — agent consumption contract

## Advisory marketplace check

On first AnyChat use in each host-agent session, run `"$ANYCHAT_BIN" doctor --check-upgrade --json` before the requested action. If `upgrade.status` is `update_available`, briefly recommend updating from the marketplace, then continue normally. Never auto-update, never block the user, and continue silently when the check is unavailable. Run this once per session load, not before every command.

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
3. **Major stages only.** Report at phase changes: need connect → need setup → ready to search → results ready → need confirm before Tell Jacky. Skip narrating routine tool calls.
4. **Product language.** “Local chat archive on this computer.” Never expose internal access implementation, storage paths, or component details unless the user explicitly asks for technical depth.
5. **JSON is for you, not the default chat answer.** Prefer `--format json` / `doctor --json` / `whoami --json` **between tools**; translate outcomes into one or two short human sentences (e.g. “已登录，但还不是管理员” / “还没完成第一次本机设置”).
6. **Mask secrets.** Never paste credentials or private access material into chat.

Host UIs may still show tool cards; **your written reply** must still follow this section.

## 0. Non-negotiable rules

1. **All archive ops go through the bundled `anychat` CLI.** Do not invent local storage paths, access material, or reimplement product internals.
2. **Strict scopes** — legacy WeChat scoped queries use exactly one `--mode`; source-qualified/fan-out queries use `--source` or `--all-sources` and never mix legacy scope flags.
3. **Ambiguous names** — run `resolve` first; show typed candidates (person vs group).
4. **No secrets in feedback** — no credentials, private access material, message bodies, friend names/wxids, or attachment bytes unless the user explicitly approves a redacted draft.
5. Prefer `--format json` when chaining agent steps **internally**; follow **Talk to the human** when reporting results.

## Self-intro (when user asks “what can you do?”)

Answer in product language, short bullets:

- **Connect** once with a free Portal token (`connect-anychat` / `login`).
- **Setup** local archive access on this Mac or Windows PC (`setup` + doctor).
- **Search** friends, groups, person-across-groups, global keywords, or all supported local sources together.
- **Export** transcripts, **download** supported attachments, and create a local hash-verifiable evidence bundle.
- **Tell Jacky** feature / bug / tip (always draft → user confirm → `feedback create`).

Stable sources on macOS Apple Silicon: verified WeChat profiles, iMessage, and the verified Telegram local-cache profile. Windows x64 currently retains verified WeChat support; other Windows connectors stay unavailable until their own native tests pass. Automatic first-time access varies by OS and chat-app build. Not a bot; does not send messages.

## Agent quick router

| User intent | Command / skill |
|-------------|-----------------|
| "what can anychat do / how do I use it" | Answer from this skill (self-intro); if not logged in → **connect-anychat** |
| Connect / token | **connect-anychat** → `login --token-stdin --accept-personal-use` (pipe token; never put secret on argv) |
| Health / which platform | `doctor [--json]` · upgrade: `doctor --check-upgrade` |
| First-time local access | **anychat-setup** → read `doctor --json` `setup_plan` and follow `setup_plan.agent`; the human never uses a terminal; you absorb every problem |
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
| Export transcript | `export --mode … --target … -o ./anychat-export/messages.json` |
| List attachments | `media list --mode friend --target "…" --type image\|voice\|file\|all` |
| Download one / all | `media download --id … -o dir` / `media download-all …` |
| Voice → playable | Download voice → **WAV**; **agent runs STT** (anychat has no AI/STT) |
| Tell Jacky | **tell-jacky** (confirm draft first) → `feedback create --user-confirmed` |
| Jacky replied / unread replies | `feedback inbox [--json]` → show each reply → `feedback read --update-id <id>` (once per session, best-effort) |
| Saved nicknames | `alias set/list/rm` · `recents` |

## §B. Resolve binary

1. `$ANYCHAT_BIN` if set, executable, and matching native platform  
2. Active plugin runtime (highest priority for agent plugins):  
   - Claude plugin root / cache: `…/anychat-cli/<latest-ver>/bin/<platform>/anychat` or `$CLAUDE_PLUGIN_ROOT/bin/<platform>/anychat[.exe]`  
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
- On repeated failures, offer **tell-jacky** once (opt-in).  
- Do not spam feedback or re-login prompts.

## Product language

Describe as a **local chat archive** helper. Do not discuss internal access implementation with the user. See **Talk to the human** above.
