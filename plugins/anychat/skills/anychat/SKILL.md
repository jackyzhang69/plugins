---
name: anychat
description: >-
  READ THIS FIRST for AnyChat. Local chat archive helper: search my chats,
  chat with X, what did X say in groups, search all chats for keyword,
  search social media chats, cross-platform chat history, contacts,
  identity, export messages with X, export my chat with X, save group
  transcript, download images from group Y, download images from chat with X,
  download all images from, list voice messages, download voice messages,
  transcribe this voice, turn voice into Chinese text, attachments in group Y,
  export that PDF, follow this person / group for new messages, save a topic,
  check a topic, write this down, what did we decide, remember this point,
  connect / log in / save my portal token, anychat setup, tell Jacky, report
  this bug / file a bug report, feature request, note this as a tip,
  connect with Jacky, join code from Jacky, request live support,
  same person across apps, iMessage, Telegram, other local chats,
  evidence pack, proof of what was said.
  One discovery file: playbooks in references/. The capability list below is
  the full command surface for this version; `anychat commands --json` confirms
  it. Ask the live CLI only for volatile catalogs (friend and group lists, messages).
when_to_use: |-
  Load on plugin start; reload whenever a user asks anything anychat-related.
  Trigger phrases: "search my chats", "chat with X", "group Y",
  "what did X say in groups", "search all chats for keyword",
  "search social media chats", "cross-platform chat history",
  "contacts", "identity", "same person across apps",
  "export messages with X", "export my chat with X",
  "save group transcript", "download images from group Y",
  "download images from chat with X", "download all images from",
  "list voice messages", "download voice messages",
  "transcribe this voice", "turn voice into Chinese text",
  "attachments in group Y", "export that PDF",
  "follow this person", "save a topic", "check a topic", "any new messages from",
  "write this down", "what did we decide", "remember this point",
  "connect to anychat / set up anychat", "log in to anychat / save my portal token",
  "anychat setup", "tell Jacky", "tell Jacky about this",
  "report this bug / file a bug report", "feature request for AnyChat",
  "note this as a tip", "connect with Jacky", "join code from Jacky",
  "request live support", "iMessage", "Telegram", "other local chats",
  "evidence pack", "proof of what was said",
  "cloud assistant", "use my chats in a cloud agent",
  "聊到 Muse / 云端助手", "在云端助手里查聊天".
  Bare "@anychat" / "what can anychat do" / "how do I use anychat":
  if not logged in, follow references/connect.md; otherwise answer from the capability list.
---

# AnyChat plugin — agent consumption contract

Load this on plugin start and whenever the user asks anything AnyChat-related.

AnyChat is a **local chat archive** helper: the user searches and exports **their own**
chat history on this computer. The archive stays on this computer; a cloud
assistant receives only the bounded snapshot the user asked for, and AnyChat
keeps no copy of it. Portal login, explicitly connected content-free support
status, and optional redacted Tell Jacky feedback may leave the device.

## Talk to the human

On the first session after install, read [get-started](references/get-started.md)
before the first user-visible reply. Brief the human in plain language — what
AnyChat does and what to try first — not a command list. After a version bump,
read [whats-new](references/whats-new.md), tell the human what changed in one
breath, then resume their original intent without asking them to repeat the
request. Never ask the human to run doctor as homework.

Use everyday words. Say what you are doing and what they need next — not binary
paths, `--help`, raw JSON, or internal field names. JSON stays between tools.
Never paste credentials. Describe as a **local chat archive on this computer**.

Voice can be listed and downloaded as playable audio. AnyChat does not turn
voice into Chinese or any other text; that is intentional. When the human wants
a transcript, this host agent downloads the WAV and transcribes it locally. Do
not treat missing speech-to-text as a product gap.

## Updates

`anychat` may print one stderr line with schema `jz.plugin.update_notice.v1`. It never blocks a command. Finish what the user asked first, then tell them once, in plain words, that a newer version is available and ask whether to update now. On yes: refresh this plugin through the host (Claude Code: `claude plugin marketplace update jacky-plugins`, then `claude plugin update anychat@jacky-plugins`; Codex: `codex plugin marketplace upgrade jacky-plugins`, then `codex plugin add anychat@jacky-plugins`; other hosts: attach the new `plugins/anychat/` tree). Then run `doctor --repair-install` with the binary from the **new** package, not the folder this session started from (Claude Code: `~/.claude/plugins/cache/jacky-plugins/anychat/<latest>/bin/<platform>/anychat`; Codex: `~/.codex/plugins/cache/jacky-plugins/anychat/<latest>/bin/<platform>/anychat`; `<latest>` is the notice's `latest` value or the highest version folder present; `<platform>` is `darwin-arm64`, `linux-x64` or `win32-x64`). Read `references/whats-new.md` from that folder and tell the user in one or two sentences what changed and that the host loads it fully after a restart. For the rest of this session use that folder for every command and reference; the old version folder is gone. On no: do not ask again in this session.

## Before first use — platform prerequisites

Check these once, before the first real request. They are the difference between
"AnyChat is broken" and a two-minute fix.

- **Check first.** `"$ANYCHAT_BIN" doctor --json`. When a newer version exists the
  CLI prints a notice; follow **Updates** above, and `"$ANYCHAT_BIN" update --json`
  installs the official package once the human agrees. Releases from 0.1.99 also
  refresh a stale local authorization cache, so an upgrade is never left showing
  "expired".
- **macOS: Full Disk Access belongs to the app that runs this agent, not to
  AnyChat.** macOS credits the archive read to the host app that launched the
  CLI. System Settings → Privacy & Security → Full Disk Access → add that host
  app (DeepSeek Harness, Claude, ChatGPT, Codex, Cherry Studio, …) → quit and
  reopen it. Granting the `anychat` binary itself does not work.
  Verify: `"$ANYCHAT_BIN" sources list --json` shows `archive_permission: granted`,
  then `"$ANYCHAT_BIN" provision --json` reaches `ready`.
- **Windows: one supported chat-app build, UAC allowed, chat app signed in.**
  Install only the build the product lists, approve the UAC prompt when setup
  asks, and keep the chat app signed in and running during setup.

When setup still fails, map the support code to the first action instead of
guessing:

| support code | First action |
|---|---|
| `E_CONNECT_REQUIRED` | [connect](references/connect.md), then re-run the same request |
| `E_ENTITLEMENT_REQUIRED` | update first, then re-run; no reinstall |
| `E_ARCHIVE_NOT_FOUND` | re-check the macOS / Windows prerequisites above; never reinstall the chat app or delete chat data |
| `E_LOCAL_ACCESS_IN_PROGRESS` | let the current scan finish; do not start a second one |
| `E_LOCAL_KEY_NOT_FOUND` | do not retry the identical scan and do not reinstall or delete chat data; prepare Tell Jacky with the access diagnosis, then follow [tell-jacky](references/tell-jacky.md) |
| `E_UPDATE_FAILED` | keep the installed version, retry once, then Tell Jacky |

## When the user asks "what can you do?"

Answer from the capability list below, which is the full command surface for
this version, and translate it into short product bullets (search, export,
follow topics, link identities, download voice, iMessage and Telegram, evidence
packs, live support with Jacky, Tell Jacky). `"$ANYCHAT_BIN" commands --json`
confirms the list. If they ask to transcribe this voice or turn voice into
Chinese text, download the audio and transcribe it here; do not wait for
AnyChat to convert speech. If not logged in, run [connect](references/connect.md)
first.

## Capability list — every public command

One line per command with its key flags and stdin format; every command is listed here, so this list is the full surface.

- `login` saves the Portal token (`--token-stdin` reads the raw token as one line on stdin; `--accept-personal-use`); `logout` removes it; `whoami` shows who is signed in.
- `doctor` reports readiness and next steps (`--json`); `doctor --repair-setup` fixes stale setup state; `doctor --repair-install` makes the package this binary belongs to the only installed copy; `status` is the short setup summary.
- `update` installs the official newer package after the human agrees (`--json`).
- `provision` is the single setup flow (`--json`, `--resign-consent`, `--restore-app`, `--supply-stdin` reads one JSON object of typed host-agent facts on stdin, `--complete-remaining`); `resume` continues the original request after setup (`--token-stdin` reads the opaque `resume_token` as one line on stdin).
- `stage` writes a bounded snapshot for a cloud assistant (`--days`, `--clear`, `--json`).
- `friends list` lists friends (`--query`, `--type`, `--limit`); `groups list` lists groups (`--query`, `--limit`); `groups members` lists one group's members (`--query`, `--limit`); `contacts card` shows one contact's profile card (`--query`); `resolve` turns an ambiguous name into typed candidates (`--query`); `sessions` lists recent chat sessions (`--limit`); `recents` lists recently used targets (`--limit`).
- `query` reads messages in one scope or one source (`--mode`, `--target`, `--group`, `--person`, `--keyword`, `--days`, `--limit`, `--context`, `--format`, `--source`, `--all-sources`).
- `search` finds a keyword or a linked person across all conversations (`--keyword`, `--person`, `--days`, `--limit`, `--format`, `--all-sources`).
- `export` writes a transcript file for one scope (`--mode`, `--target`, `--group`, `--days`, `--limit`, `--format`, `--output`).
- `media list` lists attachments in one scope (`--mode`, `--target`, `--group`, `--type`, `--days`, `--limit`); `media download` saves one attachment by id (`--id`, `--output`); `media download-all` saves every attachment matching a scope, never by id (`--mode`, `--target`, `--group`, `--type`, `--days`, `--limit`, `--output`).
- `evidence create` builds a local evidence bundle (`--output`, `--keyword`, `--days`, `--source`, `--all-sources`); `evidence verify` checks one (`--bundle`).
- `feedback preview` shows the exact draft and sends nothing (`--type`, `--title`, `--description`); `feedback create` sends it after the human confirms (`--type`, `--user-confirmed`, `--confirmation-binding`); `feedback inbox` shows unread replies (`--limit`); `feedback read` marks one reply read (`--update-id`); `feedback status` shows one feedback's delivery (`--id`); `feedback list` lists sent feedback (`--limit`).
- `pair request` asks for a live support connection (`--user-confirmed`, `--request-key`, `--greeting-file`, `--timeout-seconds`); `pair join` joins with the code from Jacky's assistant (`--code`, `--user-confirmed`); `pair status` shows whether a connection is open.
- `pair send` sends one message (`--message-file`, `--idempotency-key`, `--phase`, `--reply-to`); `pair receive` waits for one peer message (`--timeout-seconds`); `pair next` optionally sends a reply, optionally acknowledges a message, then waits for the next peer message (`--message-file`, `--idempotency-key`, `--ack-message-id`, `--reply-to`, `--phase`, `--timeout-seconds`).
- `pair ack` acknowledges a handled peer message (`--message-id`); `pair inbox` lists unread instructions; `pair read` marks one instruction read (`--message-id`); `pair snapshot` sends the current product envelope (`--reply-to`, `--idempotency-key`, `--envelope-json`); `pair result` reports an action's outcome (`--ok`, `--error`, `--summary`, `--reply-to`, `--idempotency-key`, `--envelope-json`); `pair close` ends the connection (`--reason`).
- `alias set` saves a nickname for a query (`<NAME>`, `--mode`, `--target`, `--group`); `alias list` lists nicknames; `alias rm` deletes one (`<NAME>`).
- `sources detect` finds which local chat sources are present (`--json`); `sources list` lists registered sources with status and next actions (`--json`); `sources status` reports status for all sources or one (`<SOURCE>`, optional); `sources connect` prepares one source (`<SOURCE>`, required); `sources sync` runs an incremental sync for one source (`<SOURCE>`, required); `sources remove` drops a runtime-registered source from this session only (`<SOURCE>`, required).
- `identity list` and `identity suggest` show the cross-app person graph and candidates; `identity link` previews or writes a link (`--person-label`, `--source`, `--raw-id`, `--confirm`); `identity confirm` and `identity unlink` act on one account (`--source`, `--raw-id`); `identity reset` clears all links (`--yes`).
- `topic save` follows a person or conversation for new messages (`--name`, `--person`, `--conversation`, `--keyword`, `--source`); `topic list` lists topics; `topic show` and `topic check` act on one topic (`--topic-id`); `topic rm` deletes one (`--topic-id`, `--yes`).
- `notes save` keeps confirmed claims for a topic (`--topic-id`, `--name`, `--claim`, `--draft`, `--yes`); `notes list` lists notes; `notes show` and `notes touch` act on one note (`--note-id`); `notes rm` deletes one (`--note-id`, `--yes`).
- `commands` prints this surface as JSON (`--json`).

## Agent router — intents

| User intent | Host does | Human may be asked |
|---|---|---|
| "what can anychat do / how do I use it" | Answer from the capability list; `"$ANYCHAT_BIN" commands --json` confirms it; translate to product language | Connect once if not logged in ([connect](references/connect.md)) |
| search my chats / chat with X / group Y / what did X say in groups / search all chats for keyword / cross-platform chat history | [query](references/query.md): resolve ambiguous names first; prefer linked-identity search across sources | Disambiguate person vs group; confirm scope when ambiguous |
| export messages / save group transcript / export that PDF | [export](references/export.md) | Output location preference when needed |
| download images / list voice messages / download voice messages / transcribe this voice | [media](references/media.md); host agent transcribes the WAV | Which conversation when ambiguous |
| follow this person / save a topic / check a topic / any new messages from | Topic save/check flow per [command-router](references/command-router.md) | Confirm proposed topic name `{人或群} {事}` |
| write this down / what did we decide / remember this point | Notes save after explicit ask | Confirm drafted claim before save |
| connect / anychat setup / log in / save my portal token | [connect](references/connect.md) or [setup](references/setup.md) for 开通本机档案 | Token file path, OS password, or sign-in when the product requests |
| tell Jacky / report bug / feature / tip | [tell-jacky](references/tell-jacky.md) | Confirm exact draft before send |
| request live support / connect with Jacky / pair session | [pair-session](references/pair-session.md) | The human's request to connect is consent for this support conversation |
| identity / same person across apps | [command-router](references/command-router.md) identity list/suggest/link | Confirm the link |
| iMessage / Telegram / WhatsApp (macOS experimental; Windows unsupported stub) / other local chats | [command-router](references/command-router.md) sources detect/status/connect | Which app when ambiguous |
| evidence pack / proof of what was said | [command-router](references/command-router.md) evidence create/verify | Output location when needed |
| running in a cloud assistant that cannot execute here / 在云端助手（如 Muse）里查聊天 | [cloud](references/cloud.md): `stage --days <n> --json`, upload `files[]`, answer with window + coverage stated | One consent sentence before the first upload |

Playbooks: [connect](references/connect.md), [setup](references/setup.md),
[query](references/query.md), [media](references/media.md),
[export](references/export.md), [tell-jacky](references/tell-jacky.md),
[pair-session](references/pair-session.md).

Command router and CLI paths (not for first-session orientation):
[references/command-router.md](references/command-router.md).

## Command surface check (fail-closed)

```bash
"$ANYCHAT_BIN" commands --json
```

Confirms the capability list above for the installed binary; a command missing
from the dump is not available. Volatile catalogs (friend/group lists, live
messages) are **not** in that dump; ask the live CLI for those.

Session-start advisory check (feedback inbox) is host-only —
see [command-router](references/command-router.md). Never block the user's
request on advisory failure.

## 0. Non-negotiable rules

1. **All archive ops go through the bundled `anychat` CLI.** Do not invent local storage paths, access material, or reimplement product internals.
2. **Strict scopes** — scoped queries use exactly one `--mode`; source-qualified/fan-out queries use `--source` or `--all-sources` and never mix legacy scope flags.
3. **Ambiguous names** — run `resolve` first; show typed candidates (person vs group).
4. **No secrets in feedback** — no credentials, private access material, message bodies, friend names/wxids, or attachment bytes unless the user explicitly approves a redacted draft.
5. Prefer `--format json` when chaining agent steps **internally**; follow **Talk to the human** when reporting results. During 开通本机档案, speak only `say_to_user`.

## §B. Resolve binary

Prefer the packaged binary next to this skill
(`…/plugins/anychat/current/bin/<platform>/anychat`), not a leftover PATH winner.

1. `$ANYCHAT_BIN` if set, executable, matching native platform, and not older than the canonical current package
2. Compare the active plugin runtime with the canonical current package and use the newer valid binary (active runtime wins a version tie):
   - Claude plugin root / cache: `…/anychat/<latest-ver>/bin/<platform>/anychat` or `$CLAUDE_PLUGIN_ROOT/bin/<platform>/anychat[.exe]`
   - DeepSeek Harness bundle: this skill's base directory is `<bundle>/skills/anychat`; the binary is `<bundle>/bin/<platform>/anychat[.exe]` (two directories up from the skill base)
   - Canonical current: `~/.jackyzhang.app/plugins/anychat/current/bin/<platform>/anychat`
3. Canonical standalone installation:
   - macOS: `$HOME/.local/bin/anychat` · Windows: `%USERPROFILE%\.local\bin\anychat.exe` (verify it is not older than the active plugin)
4. `command -v anychat` / `where anychat` (last resort fallback)

Export `ANYCHAT_BIN` once per session. On Windows use `anychat.exe`.

## Execution boundaries

| Work | Where |
|------|--------|
| Unlock / query / export / media copy | **Local CLI** (user machine) |
| Login entitlement / Tell Jacky submit / live connection with Jacky's assistant | Portal API host `account.jackyzhang.app` — machine HTTPS only. Never open this host in a browser as a login page. |
| Chat message bodies | **Local by default.** In a cloud assistant, only the staged snapshot is uploaded ([cloud](references/cloud.md)); no archive db, key or token file ever is, and AnyChat keeps no copy |

## After success (stickiness, light touch)

- Offer at most **one** soft “try next” suggestion.
- During 开通本机档案, do not offer Tell Jacky unless `provision` says `blocked` and `offer_tell_jacky`. After the archive is ready, offer it once only if they ask or the same non-setup failure repeats.
- Do not spam feedback or re-login prompts.

## Product language

Describe as a **local chat archive**. Discuss only product capabilities, requirements, consent, and results. See **Talk to the human** above.
