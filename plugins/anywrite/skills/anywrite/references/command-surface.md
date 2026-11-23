<!-- generated-by: assert-skill-surface.py ; do not edit -->
# anywrite command reference

Generated from `anywrite commands --json` (version `0.1.9`). Every public command is listed.

## Global options

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted for compatibility; every command prints JSON |

## `anywrite commands`

Print the public command surface (names, flags, one-line meanings) of this exact build as JSON; read-only and offline

No arguments.

## `anywrite doctor`

Check that login, this computer's approval and the service are ready, and report pending writes, without installing anything; with `--repair-install`, instead make the running package the only installed copy (keeps login and saved methods)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--repair-install` | flag | no |  | Reinstall the running package as the only installed copy: switches `current`, deletes older trees and caches; needs no login or network |

## `anywrite whoami`

Resolve the connected account and return its masked subject; changes nothing

No arguments.

## `anywrite login`

Connect this machine to the shared user login by reading one token from stdin; writes the shared login slot and returns a readiness result

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--token-stdin` | flag | no |  | Required: read the token from protected stdin, never from arguments |

## `anywrite logout`

Remove the shared user login from this machine (other plugins lose it too) and return the local result

No arguments.

## `anywrite definitions search`

Look for existing saved writing methods by scope or text before creating one; read-only. A failed search is never "nothing found"

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--scope-file` | option | no |  | Scope facets JSON file path (partial or complete), or `-` for stdin |
| `--query` | option | no |  | Free-text query |
| `--include-archived` | flag | no |  | Also return archived methods |
| `--limit` | option | no |  | Page size, 1 to 10 |
| `--cursor` | option | no |  | Cursor returned by the previous page |

## `anywrite definitions show`

Read one saved writing method, the active revision unless a revision is given; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | yes |  | Definition ID to read |
| `--revision` | option | no |  | Revision number to read instead of the active one |

## `anywrite definitions validate`

Validate a local draft against the method schema and the safety checks; never contacts the server and saves nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--file` | option | yes |  | Draft definition JSON file path, or `-` for stdin |

## `anywrite definitions save`

Create a new saved writing method from a draft the user confirmed; without confirmation returns a preview and its hash and saves nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--file` | option | yes |  | Draft definition JSON file path, or `-` for stdin |
| `--confirmed` | flag | no |  | The user confirmed the exact draft shown |
| `--confirm-hash` | option | no |  | Hash from the preview the user confirmed |

## `anywrite definitions replace`

Append a new revision to an existing saved method from a draft the user confirmed; without confirmation returns a preview and saves nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | yes |  | Definition ID to revise |
| `--expected-revision` | option | yes |  | Revision the draft was based on; a mismatch is refused |
| `--file` | option | yes |  | Draft definition JSON file path, or `-` for stdin |
| `--confirmed` | flag | no |  | The user confirmed the exact draft shown |
| `--confirm-hash` | option | no |  | Hash from the preview the user confirmed |

## `anywrite definitions export`

Export one saved writing method with all its revisions, to stdout or a new private file; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | yes |  | Definition ID to export |
| `--output` | option | no |  | Write the export to a new private file instead of stdout |

## `anywrite definitions archive`

Archive a saved writing method after the user agreed; reversible with `definitions restore`

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | yes |  | Definition ID to archive |
| `--expected-revision` | option | yes |  | Revision the user saw; a mismatch is refused |
| `--confirmed` | flag | no |  | The user agreed to archive it |

## `anywrite definitions restore`

Restore an archived writing method after the user agreed

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | yes |  | Definition ID to restore |
| `--expected-revision` | option | yes |  | Revision the user saw; a mismatch is refused |
| `--confirmed` | flag | no |  | The user agreed to restore it |

## `anywrite definitions forget`

Permanently delete a saved writing method and every revision after the user confirmed; cannot be undone

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | yes |  | Definition ID to delete |
| `--expected-revision` | option | yes |  | Revision the user saw; a mismatch is refused |
| `--confirm-id` | option | yes |  | Repeat the definition id to confirm the deletion target |
| `--confirmed` | flag | no |  | The user confirmed the deletion |

## `anywrite operations show`

Show what the service recorded for one idempotency key; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--key` | option | yes |  | Idempotency key of the earlier write |

## `anywrite feedback`

Preview a feedback draft for Jacky, or send it after the user confirmed the exact text; nothing is attached automatically

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | yes |  | Feedback draft JSON file path (type, title, description, idempotency_key), or `-` for stdin |
| `--user-confirmed` | flag | no |  | Send for real after the user saw the exact draft; without it only a preview is returned |

## `anywrite device status`

Show whether this computer is approved on the account; read-only

No arguments.

## `anywrite device remove-this`

Remove this computer from the account and free its slot after the user confirmed

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--confirmed` | flag | no |  | The person has confirmed removing this computer |

## `anywrite device reset-key`

Set this computer up again after it was removed in the account page, once the user agreed

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--confirmed` | flag | no |  | The person has said yes to using this computer again |

## `anywrite feedback-list`

List the feedback belonging to this account for AnyWrite; read-only

No arguments.

## `anywrite feedback-status`

Read one feedback item and its current status; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | yes |  | Feedback ID returned when it was sent |
