<!-- generated-by: assert-skill-surface.py ; do not edit -->
# anychat command reference

Generated from `anychat commands --json` (version `0.1.106`). Every public command is listed.

## `anychat login`

Save Portal token (one-time)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--token-stdin` | flag | no |  | Read token from stdin (non-echoing when a TTY; primary agent path) |
| `--accept-personal-use` | flag | no |  | Accept personal-use terms |

## `anychat logout`

Remove local token

No arguments.

## `anychat whoami`

Show Portal identity (no secrets)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Print the result as JSON |

## `anychat doctor`

Show whether AnyChat is ready on this computer and what to do next; changes nothing unless a repair flag is given

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Print the result as JSON |
| `--repair-setup` | flag | no |  | Reconcile historical WeChat setup recording from live archive evidence. Agent-operable repair for hosts stuck with setup_complete=false while the archive is already queryable. Never hand-edit config.json |
| `--repair-install` | flag | no |  | Make the package this binary belongs to the only installed copy under the AnyChat plugin folder, removing older versions and leftovers (keeps login, settings and chat data); needs no login |

## `anychat update`

Explicit host-agent update from the verified official package

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Print the result as JSON (status, active binary and skill paths) |

## `anychat stage`

Write a bounded snapshot for a cloud host agent (staging contract)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--days` | option | no |  | Days of history to include (0 = entire archive) |
| `--clear` | flag | no |  | Remove every staged part and the manifest, then stop |
| `--json` | flag | no |  | Print the result as JSON |

## `anychat status`

Local setup status summary

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Print the result as JSON |

## `anychat provision`

First-run: the single setup state machine for host agents

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Print the result as JSON (the setup envelope with next steps) |
| `--resign-consent` | flag | no |  | macOS: the human agreed to re-sign their local chat app |
| `--restore-app` | flag | no |  | macOS: restore the official chat-app signature from the local backup |
| `--supply-stdin` | flag | no |  | Read typed host-agent facts from stdin. Paths never go on argv |
| `--complete-remaining` | flag | no |  | Intentionally resume scanning remaining message parts after partial readiness |

## `anychat resume`

Continue an ordinary request after AnyChat reports readiness

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--token-stdin` | flag | no |  | Read the opaque continuation from stdin so it never enters argv |

## `anychat friends list`

List friends by name; returns names and ids only, never messages

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--query` | option | no |  | Only list friends whose name contains this text |
| `--type` | option | no | `person`, `official`, `filetransfer`, `all` | Contact kind: person, official, filetransfer or all |
| `--limit` | option | no |  | Maximum number of friends to list |
| `--json` | flag | no |  | Print the friends as JSON |

## `anychat groups list`

List groups by name; returns names and ids only, never messages

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--query` | option | no |  | Only list groups whose name contains this text |
| `--limit` | option | no |  | Maximum number of groups to list |
| `--json` | flag | no |  | Print the groups as JSON |

## `anychat groups members`

Group member roster (D6)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--query` | option | yes |  | Group name to look up |
| `--limit` | option | no |  | Maximum number of members to list |
| `--json` | flag | no |  | Print the members as JSON |

## `anychat contacts card`

Show one contact's profile card; returns profile fields only, never messages

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--query` | option | yes |  | Name of the contact to look up |
| `--json` | flag | no |  | Print the card as JSON |

## `anychat resolve`

Resolve ambiguous names to typed candidates

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--query` | option | yes |  | Name or fragment to resolve (friend, group or person) |
| `--json` | flag | no |  | Print the typed candidates as JSON |

## `anychat search`

Global keyword search (no target required)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--keyword` | option | no |  | Text to search for. Optional when --person names someone you have already linked |
| `--keywords` | option | no |  | Match any term. Repeat the flag or pass a comma-separated list |
| `--rerank` | flag | no |  | Explicitly allow remote Jev answerhood reranking of returned candidates |
| `--person` | option | no |  | Someone you have already linked (`anychat identity list`). Searches every account you confirmed for them, on every platform |
| `--days` | option | no |  | Look back this many days |
| `--limit` | option | no |  | Maximum number of results to return |
| `--format` | option | no | `text`, `json`, `md` | Output format: text, json or md |
| `--output` | option | no |  | Write the results to this file instead of the screen |
| `--source` | option | no |  | Query one registered source by id (see `anychat sources list`) |
| `--all-sources` | flag | no |  | Fan out across every registered source (explicit; never implicit) |
| `--continuation` | option | no |  | Continuation token from a previous fan-out envelope (pagination) |
| `--account` | option | no |  | Explicit account for sources with multiple local accounts |

## `anychat sessions`

Recent chat sessions (D4)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--limit` | option | no |  | Maximum number of sessions to list |
| `--json` | flag | no |  | Print the sessions as JSON |

## `anychat export`

Export transcript (alias of query with file-friendly defaults)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--mode` | option | yes | `friend`, `group`, `person-in-groups`, `person-in-group`, `me-in-groups`, `me-in-group`, `multi-in-group` | Which conversations to export: friend, group, person-in-groups, person-in-group, me-in-groups, me-in-group or multi-in-group |
| `--target` | option | no |  | Friend, group or person name, depending on the mode |
| `--group` | option | no |  | Group name; required for the person-in-group, me-in-group and multi-in-group modes |
| `--days` | option | no |  | Export messages from the last N days |
| `--since-unix` | option | no |  | Incremental (E4): only messages with create_time >= this unix seconds (overrides --days) |
| `--keyword` | option | no |  | Only export messages containing this text |
| `--keywords` | option | no |  | Match any term. Repeat the flag or pass a comma-separated list |
| `--rerank` | flag | no |  | Explicitly allow remote Jev answerhood reranking of returned candidates |
| `--type` | option | no | `all`, `text`, `voice`, `image`, `file`, `link` | Only export this kind of message (all, text, voice, image, file, link) |
| `--limit` | option | no |  | Maximum number of messages to export |
| `--format` | option | no | `text`, `json`, `md` | Output format: text, json or md |
| `--output` | option | no |  | File to write the exported transcript to; creates the folder when missing |

## `anychat query`

Scoped query (friend / group / person-in-groups / …)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--mode` | option | no | `friend`, `group`, `person-in-groups`, `person-in-group`, `me-in-groups`, `me-in-group`, `multi-in-group` | Required on the legacy WeChat path (omit only with --source/--all-sources) |
| `--target` | option | no |  | Friend, group, or person depending on mode |
| `--group` | option | no |  | Required for person_in_group / me_in_group / multi_in_group |
| `--person` | option | no |  | Extra person(s) for multi_in_group (repeatable) |
| `--days` | option | no |  | Look back this many days |
| `--since-unix` | option | no |  | Incremental (E4): only messages with create_time >= this unix seconds (overrides --days) |
| `--keyword` | option | no |  | Only return messages containing this text |
| `--keywords` | option | no |  | Match any term. Repeat the flag or pass a comma-separated list |
| `--rerank` | flag | no |  | Explicitly allow remote Jev answerhood reranking of returned candidates |
| `--type` | option | no | `all`, `text`, `voice`, `image`, `file`, `link` | Only return this kind of message (all, text, voice, image, file, link) |
| `--limit` | option | no |  | Maximum number of messages to return |
| `--context` | option | no |  | Include ±N neighboring messages around keyword hits (F5) |
| `--format` | option | no | `text`, `json`, `md` | Output format: text, json or md |
| `--output` | option | no |  | Write the results to this file instead of the screen |
| `--source` | option | no |  | Query one registered source by id (see `anychat sources list`) |
| `--all-sources` | flag | no |  | Fan out across every registered source (explicit; never implicit) |
| `--continuation` | option | no |  | Continuation token from a previous fan-out envelope (pagination) |
| `--account` | option | no |  | Explicit account for sources with multiple local accounts |

## `anychat media list`

List attachments under a scoped chat (same --mode rules as query)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--mode` | option | no | `friend`, `group`, `person-in-groups`, `person-in-group`, `me-in-groups`, `me-in-group`, `multi-in-group` | Required on the legacy WeChat path (omit only with --source/--all-sources) |
| `--target` | option | no |  | Friend, group or person name, depending on the mode |
| `--group` | option | no |  | Group name, for the person-in-group, me-in-group and multi-in-group modes |
| `--type` | option | no | `all`, `image`, `voice`, `video`, `file`, `link` | Attachment kind: all, image, voice, video, file or link |
| `--days` | option | no |  | Look back this many days |
| `--limit` | option | no |  | Maximum number of attachments to list |
| `--json` | flag | no |  | Print the attachments as JSON |
| `--source` | option | no |  | Resolve through one registered source by id (JSON envelope) |
| `--all-sources` | flag | no |  | Fan out across every registered source (JSON envelope) |
| `--continuation` | option | no |  | Continuation token from a previous fan-out envelope |
| `--account` | option | no |  | Explicit account for sources with multiple local accounts |

## `anychat media download`

Download one attachment by id (from media list / query JSON)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | yes |  | Attachment id from `media list` or query JSON |
| `--output` | option | no |  | Folder to save the attachment into |
| `--json` | flag | no |  | Machine-readable result (includes playable_path for voice) |
| `--source` | option | no |  | Resolve through one registered source by id (metadata report in v1) |
| `--account` | option | no |  | Explicit account for sources with multiple local accounts |

## `anychat media download-all`

Download all attachments matching a scoped query

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--mode` | option | yes | `friend`, `group`, `person-in-groups`, `person-in-group`, `me-in-groups`, `me-in-group`, `multi-in-group` | Which conversations to download from: friend, group, person-in-groups, person-in-group, me-in-groups, me-in-group or multi-in-group |
| `--target` | option | no |  | Friend, group or person name, depending on the mode |
| `--group` | option | no |  | Group name, for the person-in-group, me-in-group and multi-in-group modes |
| `--type` | option | no | `all`, `image`, `voice`, `video`, `file`, `link` | Attachment kind: all, image, voice, video, file or link |
| `--days` | option | no |  | Look back this many days |
| `--limit` | option | no |  | Maximum number of attachments to download |
| `--output` | option | no |  | Folder to save the attachments into |

## `anychat evidence create`

Query local sources and atomically create a private evidence bundle

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--keyword` | option | no |  | Only include messages containing this text |
| `--days` | option | no |  | Look back this many days |
| `--limit` | option | no |  | Per-source page size, not a global result cap |
| `--source` | option | no |  | Source id to collect from, from `sources list` |
| `--all-sources` | flag | no |  | Collect from every registered source |
| `--account` | option | no |  | Account to use when the source has several local accounts |
| `--output` | option | yes |  | New folder to write the bundle into; it must not exist yet |

## `anychat evidence verify`

Re-hash a bundle and reject tampering, extra files, and unsafe paths

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--bundle` | option | yes |  | Bundle folder to verify |

## `anychat feedback preview`

Preview the exact draft and safe diagnostic context. Never sends

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--type` | option | yes | `feature-request`, `bug-report`, `knowledge-tip` | Kind of feedback: feature-request, bug-report or knowledge-tip |
| `--title` | option | no |  | Short title; omit when --draft-json supplies it |
| `--description` | option | no |  | What happened or what is wanted; omit when --draft-json supplies it |
| `--draft-json` | option | no |  | UTF-8 JSON object with title+description. `-` reads stdin |
| `--url` | option | no |  | Related page or address, if any |
| `--context-json` | option | no |  | Extra JSON context to attach to the draft |
| `--image` | option | no |  | Local image path to attach. Repeatable |
| `--attach-access-diagnosis` | flag | no |  | Include a recent redacted access diagnosis when one exists |
| `--no-access-diagnosis` | flag | no |  | Do not attach access diagnosis even if present |

## `anychat feedback create`

Create feedback (agent must show draft + get user confirm first). Prefer attaching the redacted access diagnosis after provision fails

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--type` | option | yes | `feature-request`, `bug-report`, `knowledge-tip` | Kind of feedback: feature-request, bug-report or knowledge-tip |
| `--title` | option | no |  | Short title; omit when --draft-json supplies it |
| `--description` | option | no |  | What happened or what is wanted; omit when --draft-json supplies it |
| `--draft-json` | option | no |  | UTF-8 JSON object with title+description. Use this on Windows so Chinese is not passed through a console code page. `-` reads stdin |
| `--url` | option | no |  | Related page or address, if any |
| `--context-json` | option | no |  | Extra JSON context to attach to the draft |
| `--image` | option | no |  | Local image path (repeatable) |
| `--attach-access-diagnosis` | flag | no |  | Explicitly attach last redacted access diagnosis (default when file exists) |
| `--no-access-diagnosis` | flag | no |  | Do not attach access diagnosis even if present |
| `--user-confirmed` | flag | no |  | Required: caller affirms user confirmed the draft |
| `--confirmation-binding` | option | no |  | Opaque one-time binding emitted by `feedback preview` |

## `anychat feedback status`

Show the delivery status of one sent feedback; returns status only, changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | yes |  | Feedback id returned by `feedback create` |
| `--json` | flag | no |  | Print the status as JSON |

## `anychat feedback list`

List feedback sent from this computer; returns ids and titles, changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--limit` | option | no |  | Maximum number of entries to list |
| `--json` | flag | no |  | Print the entries as JSON |

## `anychat feedback inbox`

Show unread replies from Jacky (your Tell Jacky inbox). Never marks read

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--limit` | option | no |  | Maximum number of replies to show |
| `--json` | flag | no |  | Print the replies as JSON |

## `anychat feedback read`

Mark one reply read by update id (only after successfully showing it)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--update-id` | option | yes |  | Id of the reply, from `feedback inbox` |
| `--json` | flag | no |  | Print the result as JSON |

## `anychat pair request`

Request support and wait for approval; send the agent's greeting once connected

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--user-confirmed` | flag | no |  | Required: the person asked for a live support connection |
| `--request-key` | option | yes |  | Opaque key identifying this request, reused if the request is retried |
| `--greeting-file` | option | yes |  | UTF-8 file with the greeting sent once the connection opens |
| `--timeout-seconds` | option | no |  | Seconds to wait for approval before giving up |
| `--json` | flag | no |  | Print the result as JSON |

## `anychat pair join`

Join with the code from Jacky's assistant after the person agrees

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--code` | option | yes |  | Join code given by Jacky's assistant |
| `--user-confirmed` | flag | no |  | Required: the person agreed to join |
| `--json` | flag | no |  | Print the result as JSON |

## `anychat pair status`

Show whether a connection is open

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Print the connection state as JSON |

## `anychat pair send`

Send one role-neutral message; text comes from a UTF-8 file or stdin

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--message-file` | option | yes |  | UTF-8 file with the message text; `-` reads stdin |
| `--idempotency-key` | option | yes |  | Stable key reused if this send is retried |
| `--phase` | option | no | `question`, `progress`, `answer`, `needs_human`, `offline` | Message phase: question, progress, answer, needs_human or offline |
| `--reply-to` | option | no |  | Task reference this message answers |
| `--json` | flag | no |  | Print the result as JSON |

## `anychat pair receive`

Wait for a peer message; the Rust client renews bounded polls internally

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--timeout-seconds` | option | no |  | Seconds to wait for a peer message |
| `--json` | flag | no |  | Print the message as JSON |

## `anychat pair next`

Continue this support conversation without ending the host turn

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--message-file` | option | no |  | UTF-8 file with the message to send before waiting; needs --idempotency-key |
| `--idempotency-key` | option | no |  | Stable key reused if this send is retried; needs --message-file |
| `--ack-message-id` | option | no |  | Id of a peer message to acknowledge first |
| `--reply-to` | option | no |  | Task reference the message answers |
| `--phase` | option | no | `question`, `progress`, `answer`, `needs_human`, `offline` | Message phase: question, progress, answer, needs_human or offline |
| `--timeout-seconds` | option | no |  | Seconds to wait for the next peer message |
| `--json` | flag | no |  | Print the result as JSON |

## `anychat pair ack`

Acknowledge a peer message after the host agent has handled it

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--message-id` | option | yes |  | Id of the peer message to acknowledge |
| `--json` | flag | no |  | Print the result as JSON |

## `anychat pair snapshot`

Send the current product envelope

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--reply-to` | option | yes |  | Task reference returned by pair receive |
| `--idempotency-key` | option | yes |  | Stable key reused if this send is retried |
| `--envelope-json` | option | yes |  | JSON envelope file; `-` reads stdin |
| `--json` | flag | no |  | Print the result as JSON |

## `anychat pair inbox`

Show unread instructions from Jacky's assistant

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Print the instructions as JSON |

## `anychat pair read`

Mark one instruction read after showing it

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--message-id` | option | yes |  | Id of the instruction to mark read |
| `--json` | flag | no |  | Print the result as JSON |

## `anychat pair result`

Send the outcome of a requested action

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--reply-to` | option | yes |  | Task reference returned by pair receive |
| `--idempotency-key` | option | yes |  | Stable key reused if this send is retried |
| `--ok` | flag | no |  | The requested action succeeded; omit this flag to report a failure |
| `--error` | option | no |  | Short reason the requested action failed |
| `--summary` | option | no |  | Brief content-free observation from the requested check |
| `--envelope-json` | option | no |  | JSON envelope file with the result details; `-` reads stdin |
| `--json` | flag | no |  | Print the result as JSON |

## `anychat pair close`

End the connection

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--reason` | option | no |  | Short reason for closing, for example done or resolved |
| `--json` | flag | no |  | Print the result as JSON |

## `anychat alias list`

List saved aliases; returns alias names with their mode and target, changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Print the aliases as JSON |

## `anychat alias set`

Save a nickname for a conversation query so it can be reused; writes only the local alias list

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<NAME>` | positional | yes |  | Nickname to save |
| `--mode` | option | yes | `friend`, `group`, `person-in-groups`, `person-in-group`, `me-in-groups`, `me-in-group`, `multi-in-group` | Query mode the alias runs: friend, group, person-in-groups, person-in-group, me-in-groups, me-in-group or multi-in-group |
| `--target` | option | yes |  | Friend, group or person the alias points to |
| `--group` | option | no |  | Group name, for the person-in-group, me-in-group and multi-in-group modes |

## `anychat alias rm`

Delete a saved alias; changes only the local alias list

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<NAME>` | positional | yes |  | Nickname to delete |

## `anychat recents`

Recently used targets (local only)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--limit` | option | no |  | Maximum number of targets to list |
| `--json` | flag | no |  | Print the targets as JSON |

## `anychat sources detect`

Detect which registered sources are present on this machine

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Print the detected sources as JSON |

## `anychat sources list`

List registered sources with status, capabilities, and next actions

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Print the sources as JSON |

## `anychat sources status`

Structured status for all sources, or one source

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<SOURCE>` | positional | no |  | Optional source id (omit for all registered sources) |
| `--json` | flag | no |  | Print the status as JSON |

## `anychat sources connect`

Run prepare/connect steps for one source

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<SOURCE>` | positional | yes |  | Source id to connect, from `sources list` |
| `--json` | flag | no |  | Print the result as JSON |

## `anychat sources sync`

Run incremental sync for one source

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<SOURCE>` | positional | yes |  | Source id to sync, from `sources list` |
| `--json` | flag | no |  | Print the result as JSON |

## `anychat sources remove`

Remove a runtime-registered source from this session's registry (bookkeeping only — never deletes local data or configuration)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<SOURCE>` | positional | yes |  | Source id to remove, from `sources list` |
| `--json` | flag | no |  | Print the result as JSON |

## `anychat identity list`

List all mapped local persons and their aliases

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Print the persons as JSON |

## `anychat identity suggest`

Generate alias suggestions from local Contacts (unconfirmed candidates)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Print the suggestions as JSON |

## `anychat identity link`

Link an account to a person. Without --confirm this prints the draft and writes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--person-label` | option | yes |  | Label of the person to link the account to |
| `--source` | option | yes |  | Source id of the account, for example wechat or telegram |
| `--raw-id` | option | yes |  | The account's id inside that source |
| `--confirm` | flag | no |  | Apply the draft. Omit to preview the change first |

## `anychat identity confirm`

Explicitly confirm an alias link

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--source` | option | yes |  | Source id of the linked account |
| `--raw-id` | option | yes |  | The account's id inside that source |

## `anychat identity unlink`

Unlink an alias

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--source` | option | yes |  | Source id of the linked account |
| `--raw-id` | option | yes |  | The account's id inside that source |

## `anychat identity reset`

Clear all local identity graph data

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--yes` | flag | no |  | Required: confirms clearing every identity link |

## `anychat topic save`

Save a named follow-up. Must pin a person or a conversation

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--name` | option | no |  | Name for the topic; generated from the person and keyword when omitted |
| `--person` | option | no |  | Person already linked in the identity graph. Repeatable |
| `--conversation` | option | no |  | Conversation pin as `source:raw_id` (e.g. wechat:123@chatroom) |
| `--keyword` | option | no |  | Only follow messages containing this text |
| `--source` | option | no |  | Restrict the topic to this source id. Repeatable |

## `anychat topic list`

List the saved topics; returns names and ids, changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Print the topics as JSON |

## `anychat topic show`

Show one saved topic with its pins and settings; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--topic-id` | option | yes |  | Id of the topic, from `topic list` |
| `--json` | flag | no |  | Print the topic as JSON |

## `anychat topic check`

Check one topic for new messages since the last check and report them; saves the new check point

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--topic-id` | option | yes |  | Id of the topic, from `topic list` |

## `anychat topic rm`

Delete one saved topic and its notes; requires --yes

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--topic-id` | option | yes |  | Id of the topic, from `topic list` |
| `--yes` | flag | no |  | Required: confirms deleting the topic and its notes |

## `anychat notes save`

Save confirmed claims. Host drafts; this command only stores JSON

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--topic-id` | option | no |  | Topic to attach the note to |
| `--note-id` | option | no |  | Append new claims to an existing note |
| `--name` | option | no |  | Name for a new note |
| `--claim` | option | no |  | One confirmed sentence. Repeatable, at most 5 per write |
| `--draft` | option | no |  | Full anychat-notes-v1 draft JSON. Unknown fields (including excerpts) are rejected |
| `--yes` | flag | no |  | Required: without it the command only previews the draft and saves nothing |

## `anychat notes list`

List the saved notes; returns titles and ids, never message text, changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Print the notes as JSON |

## `anychat notes show`

Show one saved note with its claims; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--note-id` | option | yes |  | Id of the note, from `notes list` |
| `--json` | flag | no |  | Print the note as JSON |

## `anychat notes touch`

Mark a note as recently used so it sorts first; changes only its timestamp

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--note-id` | option | yes |  | Id of the note, from `notes list` |

## `anychat notes rm`

Delete one saved note; requires --yes

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--note-id` | option | yes |  | Id of the note, from `notes list` |
| `--yes` | flag | no |  | Required: confirms deleting the note |

## `anychat commands`

Dump the public CLI command surface as JSON (plugin skill contract)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Always JSON; accepted so `anychat commands --json` is the contract |
