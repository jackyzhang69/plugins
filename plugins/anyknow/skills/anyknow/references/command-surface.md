<!-- generated-by: assert-skill-surface.py ; do not edit -->
# anyknow command reference

Generated from `anyknow commands --json` (version `0.1.22`). Every public command is listed.

## `anyknow commands`

Print the public command surface (names, flags, one-line meanings) as JSON; read-only and offline

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Required: emit the machine-readable surface |

## `anyknow login`

Connect this machine to the shared user login by reading one token from stdin; writes the shared login slot and returns a readiness result

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--token-stdin` | flag | no |  | Required: read the token from protected stdin, never from arguments |

## `anyknow logout`

Remove the shared user login from this machine (other plugins lose it too) and return the local result

No arguments.

## `anyknow whoami`

Resolve the connected account and return its masked subject; changes nothing

No arguments.

## `anyknow status`

Report whether login, configuration and the service are ready, without installing or repairing anything; knowledge content is never returned

No arguments.

## `anyknow devices`

List the devices bound to this account; read-only

No arguments.

## `anyknow unbind`

Release one bound device by its platform id so another machine can join; returns the unbind result

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | yes |  | Platform id of the device to release, as listed by `devices` |

## `anyknow prepare`

Build a local, hash-bound preview of one knowledge change and write it to a new file; never contacts a server and saves nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | yes |  | Mutation JSON file path, or `-` for stdin |
| `--output` | option | yes |  | New file to write the prepared object to; an existing file or link is refused |

## `anyknow commit`

Submit the exact prepared change after the displayed hash matches; saves knowledge and returns the operation receipt

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | yes |  | Prepared file from `prepare` |
| `--confirm-sha256` | option | yes |  | The digest shown in the preview the user confirmed |

## `anyknow prepare-import`

Inspect and bind a complete portable exchange snapshot locally; never contacts a server and saves nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | yes |  | Snapshot JSONL file path, or `-` for stdin |
| `--output` | option | yes |  | New file to write the prepared import to; an existing file or link is refused |

## `anyknow commit-import`

Stream the exact confirmed exchange snapshot to the service using its prepared operation ID; restores knowledge and returns the receipt

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | yes |  | Prepared import file from `prepare-import` |
| `--confirm-sha256` | option | yes |  | The hash shown in the preview the user confirmed |

## `anyknow links`

Read direct confirmed relations of one knowledge item without fetching the other items; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--asset-id` | option | yes |  | Knowledge item whose relations to list |
| `--limit` | option | no |  | Page size, 1 to 100 |
| `--offset` | option | no |  | Number of relations to skip, 0 to 1000 |

## `anyknow get`

Read one saved knowledge item in its current version, with its first relations; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--asset-id` | option | yes |  | Knowledge item to read |

## `anyknow versions`

List the confirmed version history of one knowledge item; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--asset-id` | option | yes |  | Knowledge item whose history to list |

## `anyknow operation`

Read the receipt of one earlier save, import or change by its operation ID; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--operation-id` | option | yes |  | Operation ID that was bound when the change was prepared |

## `anyknow browse`

List saved knowledge by topic, tag or state without a search query; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | yes |  | Browse request JSON file path, or `-` for stdin |

## `anyknow search`

Search saved knowledge and return ranked candidates with the match status; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | yes |  | Search request JSON (query plus this-question `questions`) file path, or `-` for stdin |

## `anyknow topics`

List the topics that exist in the saved knowledge with their IDs; read-only

No arguments.

## `anyknow export`

Write the complete saved knowledge history to a new local file and return only its path, size, hash and counts

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | yes |  | Export request JSON (`format` json or markdown) file path, or `-` for stdin |
| `--output` | option | yes |  | New local file to write; an existing file or link is refused |

## `anyknow feedback`

Preview or submit product feedback to Jacky; input never auto-attaches knowledge

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | yes |  | Feedback draft JSON file path, or `-` for stdin |
| `--user-confirmed` | flag | no |  | Send for real after the user saw the exact draft; without it only a preview is returned |

## `anyknow feedback-list`

List the feedback belonging to this account and product; read-only

No arguments.

## `anyknow feedback-status`

Read one product feedback receipt and its current status; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | yes |  | Feedback ID returned when it was sent |

## `anyknow record-reuse`

Record once that saved knowledge was actually reused in the user's work; sends only an event type and usage metadata

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--operation-id` | option | yes |  | New operation UUID for this reuse; reuse the same one when retrying |

## `anyknow doctor`

Make the package this binary belongs to the only installed copy: switches `current`, deletes older trees and caches, keeps login and settings

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--repair-install` | flag | yes |  | Required: reinstall the running package as the only installed copy |

## `anyknow pair request`

Request a support session with Jacky after explicit user consent; returns the request ID. Reuse the key after uncertain delivery

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--request-key` | option | yes |  | Stable key for this request; reuse it when retrying |
| `--user-confirmed` | flag | no |  | Required: the user agreed to the support request |

## `anyknow pair request-status`

Read approval state of a support request, optionally waiting; bounded waiting never consumes or renews the request

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--request-id` | option | yes |  | Request ID returned by `pair request` |
| `--wait-seconds` | option | no |  | Seconds to wait for approval, 0 to 25 |

## `anyknow pair status`

Read the current open session or one exact session, including closed state

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--session-id` | option | no |  | Session to read; omit for the current open session |

## `anyknow pair send`

Send reviewed prose from a local UTF-8 file or stdin through A2A; delivers one message to Jacky

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--session-id` | option | yes |  | Session to send into |
| `--message-file` | option | yes |  | UTF-8 message file path, or `-` for stdin |
| `--idempotency-key` | option | yes |  | Stable key for this message; reuse it when retrying the same message |
| `--phase` | option | no | `question`, `progress`, `answer`, `needs_human`, `offline` | Kind of message: question, progress, answer, needs_human or offline |
| `--reply-to` | option | no |  | Task reference of the peer update being answered |

## `anyknow pair receive`

Wait for durable peer updates without acknowledging them

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--session-id` | option | yes |  | Session to listen on |
| `--timeout-seconds` | option | no |  | Seconds to wait for an update, 0 to 7200 |

## `anyknow pair ack`

Acknowledge only a peer update already handled by the host

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--session-id` | option | yes |  | Session the update belongs to |
| `--ack-ref` | option | yes |  | Acknowledgement reference returned with the update |

## `anyknow pair close`

Close a support session when the user asks or the agreed task ends; returns the terminal state

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--session-id` | option | yes |  | Session to close |
| `--reason` | option | yes |  | Short reason for closing |
