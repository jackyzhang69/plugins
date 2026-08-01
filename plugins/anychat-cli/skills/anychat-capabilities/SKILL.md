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
2. **Strict modes** — every query has exactly one `--mode`. Friend and group never mix.
3. **Ambiguous names** — run `resolve` first; show typed candidates (person vs group).
4. **No secrets in feedback** — no credentials, private access material, message bodies, friend names/wxids, or attachment bytes unless the user explicitly approves a redacted draft.
5. Prefer `--format json` when chaining agent steps **internally**; follow **Talk to the human** when reporting results.

## Self-intro (when user asks “what can you do?”)

Answer in product language, short bullets:

- **Connect** once with a free Portal token (`connect-anychat` / `login`).
- **Setup** local archive access on this Mac or Windows PC (`setup` + doctor).
- **Search** friends, groups, person-across-groups, global keyword.
- **Export** transcripts (text/json/md) and **download** attachments (image/voice/file/video/link cards).
- **Tell Jacky** feature / bug / tip (always draft → user confirm → `feedback create`).

Platforms: **macOS Apple Silicon** and **Windows x64**, for the **WeChat/Weixin 4.1 or newer archive family**. Automatic first-time access varies by OS and chat-app build. Not a bot; does not send messages.

## Agent quick router

| User intent | Command / skill |
|-------------|-----------------|
| "what can anychat do / how do I use it" | Answer from this skill (self-intro); if not logged in → **connect-anychat** |
| Connect / token | **connect-anychat** → `login --token-stdin --accept-personal-use` (pipe token; never put secret on argv) |
| Health / which platform | `doctor [--json]` · upgrade: `doctor --check-upgrade` |
| First-time local access | **anychat-setup** → `prepare-access` (if needed) then `setup --yes` |
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
| Voice → playable | Download voice → **WAV**; **agent runs STT** (anychat has no AI/STT) |
| Tell Jacky | **tell-jacky** (confirm draft first) → `feedback create --user-confirmed` |
| Saved nicknames | `alias set/list/rm` · `recents` |

## §B. Resolve binary

1. `$ANYCHAT_BIN` if set and executable  
2. macOS: `$HOME/.local/bin/anychat` · Windows: `%USERPROFILE%\.local\bin\anychat.exe` or `PATH`  
3. Plugin cache (platform-specific):  
   - macOS arm64: `…/anychat-cli/<ver>/bin/darwin-arm64/anychat`  
   - Windows x64: `…/anychat-cli/<ver>/bin/win32-x64/anychat.exe`  
4. Claude: `$CLAUDE_PLUGIN_ROOT/bin/<platform>/anychat[.exe]`  
5. `command -v anychat` / `where anychat` (last resort)

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
