---
name: anychat
description: >-
  READ THIS FIRST for AnyChat. Local chat archive helper: search my chats,
  chat with X, what did X say in groups, search all chats for keyword,
  search social media chats, cross-platform chat history, contacts,
  identity, export messages with X, export my chat with X, save group
  transcript, download images from group Y, download images from chat with X,
  download all images from, list voice messages, attachments in group Y,
  export that PDF, follow this person / group for new messages, save a topic,
  check a topic, write this down, what did we decide, remember this point,
  connect / log in / save my portal token, anychat setup, tell Jacky, report
  this bug / file a bug report, feature request, note this as a tip,
  connect with Jacky, join code from Jacky.
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
  "anychat setup", "tell Jacky", "tell Jacky about this",
  "report this bug / file a bug report", "feature request for AnyChat",
  "note this as a tip", "connect with Jacky", "join code from Jacky".
  Bare "@anychat" / "what can anychat do" / "how do I use anychat":
  if not logged in, follow references/connect.md; otherwise answer from live CLI.
---

# AnyChat plugin — agent consumption contract

Load this on plugin start and whenever the user asks anything AnyChat-related.

AnyChat is a **local chat archive** helper: the user searches and exports **their own**
chat history on this computer. Content stays on the machine. Only Portal login and
optional redacted Tell Jacky feedback leave the device.

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

## When the user asks "what can you do?"

Do not answer from a frozen command list. Run the live client and translate
`commands --json` into short product bullets (search, export, follow topics,
link identities, Tell Jacky). If not logged in, run [connect](references/connect.md)
first.

```bash
"$ANYCHAT_BIN" commands --json
```

## Agent router — intents

| User intent | Host does | Human may be asked |
|---|---|---|
| "what can anychat do / how do I use it" | Live `"$ANYCHAT_BIN" commands --json`; translate to product language | Connect once if not logged in ([connect](references/connect.md)) |
| search my chats / chat with X / group Y / what did X say in groups / search all chats for keyword / cross-platform chat history | [query](references/query.md): resolve ambiguous names first; prefer linked-identity search across sources | Disambiguate person vs group; confirm scope when ambiguous |
| export messages / save group transcript / export that PDF | [export](references/export.md) | Output location preference when needed |
| download images / list voice messages / attachments | [media](references/media.md) | Which conversation when ambiguous |
| follow this person / save a topic / check a topic / any new messages from | Topic save/check flow per [command-router](references/command-router.md) | Confirm proposed topic name `{人或群} {事}` |
| write this down / what did we decide / remember this point | Notes save after explicit ask | Confirm drafted claim before save |
| connect / anychat setup / log in / save my portal token | [connect](references/connect.md) or [setup](references/setup.md) for 开通本机档案 | Token file path, OS password, or sign-in when the product requests |
| tell Jacky / report bug / feature / tip | [tell-jacky](references/tell-jacky.md) | Confirm exact draft before send |
| connect with Jacky / pair session / join code from Jacky | [pair-session](references/pair-session.md) | Confirm once that Jacky's assistant may look at this machine's AnyChat status |

Playbooks: [connect](references/connect.md), [setup](references/setup.md),
[query](references/query.md), [media](references/media.md),
[export](references/export.md), [tell-jacky](references/tell-jacky.md),
[pair-session](references/pair-session.md).

Command router and CLI paths (not for first-session orientation):
[references/command-router.md](references/command-router.md).

## Live CLI discovery (fail-closed)

```bash
"$ANYCHAT_BIN" commands --json
```

Volatile catalogs (friend/group lists, live messages) are **not** in that dump.

Session-start advisory checks (upgrade hint, feedback inbox) are host-only —
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
| Login entitlement / Tell Jacky submit / live connection with Jacky's assistant | **Portal** `account.jackyzhang.app` |
| Chat message bodies | **Never uploaded** |

## After success (stickiness, light touch)

- Offer at most **one** soft “try next” suggestion.
- During 开通本机档案, do not offer Tell Jacky unless `provision` says `blocked` and `offer_tell_jacky`. After the archive is ready, offer it once only if they ask or the same non-setup failure repeats.
- Do not spam feedback or re-login prompts.

## Product language

Describe as a **local chat archive**. Discuss only product capabilities, requirements, consent, and results. See **Talk to the human** above.
