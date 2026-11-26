<!-- generated-by: assert-skill-surface.py ; do not edit -->
# anyweb command reference

Generated from `anyweb commands --json` (version `0.2.33`). Every public command is listed.

## `anyweb doctor`

Check this computer's AnyWeb setup and print the saved-credential and install state; changes nothing unless `--repair-install` is given

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--repair-install` | flag | no |  | Install the package this binary belongs to as the only live copy and delete the previous copy, other trees and old runtime caches; login, settings and saved logins are kept |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb login`

Save the shared Portal token piped on stdin after the service accepts it; prints whether this computer is signed in

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--token-stdin` | flag | no |  | Read the token from standard input; a token is never accepted as an argument |
| `--force` | flag | no |  | Replace a token that is already saved |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb logout`

Remove the saved Portal token from this computer; prints a confirmation and contacts no service

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb whoami`

Show which account this computer is signed in as, with the token masked; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb repair claims`

List this account's website-repair requests with their queue status; reads only, never starts a repair or changes a website

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb hosted-account set`

Save a website login (username and password from stdin) on this computer and as a sealed copy with the AnyWeb account service, for later fills; never prints the stored values

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--site` | option | yes |  | Website the login belongs to, for example `ircc-ee-profile` |
| `--role` | option | yes |  | Role of the login on that site, for example `default` |
| `--credentials-stdin` | flag | no |  | Read the credentials from standard input; they are never accepted as arguments |
| `--hosted-custody-confirmed` | flag | no |  | Assert that the user agreed to keep the login in both places |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb hosted-account status`

Show whether a login is saved for a site and role, with masked counts only; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--site` | option | yes |  | Website to check, for example `ircc-ee-profile` |
| `--role` | option | yes |  | Role to check, for example `default` |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb hosted-account forget`

Delete the saved login for a site and role; cannot be undone and needs the user's confirmation

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--site` | option | yes |  | Website whose login should be deleted |
| `--role` | option | yes |  | Role whose login should be deleted |
| `--user-confirmed` | flag | no |  | Assert that the user separately agreed to the deletion |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb hosted-security-answer set`

Save the answer to a security question the website just accepted, from stdin and by its opaque challenge reference; never prints the stored answer

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--challenge-ref` | option | yes |  | Opaque challenge reference from the current notice |
| `--site` | option | no |  | Website the answer belongs to; defaults to `ircc-ee-profile` |
| `--role` | option | no |  | Role of the login on that site; defaults to `default` |
| `--answer-stdin` | flag | no |  | Read the answer from standard input; it is never accepted as an argument |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb hosted-security-answer status`

Show masked counts of the saved security answers for a site and role; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--site` | option | yes |  | Website to check |
| `--role` | option | yes |  | Role to check |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb hosted-security-answer forget`

Delete the saved security answers for a site and role; cannot be undone and needs the user's confirmation

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--site` | option | yes |  | Website whose answers should be deleted |
| `--role` | option | yes |  | Role whose answers should be deleted |
| `--user-confirmed` | flag | no |  | Assert that the user separately agreed to the deletion |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb feedback create`

Send a feature request or bug report to Jacky after the user approved the exact draft; reports success only when the service confirms receipt, otherwise keeps a local copy

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--type` | option | no |  | Kind of feedback: `feature-request` or `bug-report` |
| `--title` | option | no |  | Short title of the feedback |
| `--description` | option | no |  | Full description, with no credentials or client data |
| `--user-confirmed` | flag | no |  | Assert that the user approved this exact draft |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb feedback list`

List the feedback sent from this computer; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb feedback status`

Show the status of one piece of feedback; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | no |  | Feedback id from `feedback create` or `feedback list`; the command refuses to run without it |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb feedback inbox`

Show unread replies and updates for this computer's feedback; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb feedback read`

Mark one reply or update as read after showing it

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--update-id` | option | no |  | Update id from `feedback inbox`; the command refuses to run without it |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb task start`

Start a fill from the request piped on stdin; returns what is still needed, or runs the preflight and then the fill, and changes the website only through the supported steps

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--request-stdin` | flag | no |  | Read the JSON request from standard input; a request is never accepted as an argument |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb task status`

Show the plain state of one task; changes nothing (hosts normally use the MCP step-status tool instead)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--task-ref` | option | no |  | Opaque reference of the task, as returned by `task start`; the command refuses to run without it |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb task resume`

Give a waiting task the facts, code or approval it asked for, or ask for the email code to be resent; continues the same Chrome session

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--task-ref` | option | no |  | Opaque reference of the waiting task, as returned by `task start`; the command refuses to run without it |
| `--input-stdin` | flag | no |  | Read the resume inputs from standard input; cannot be combined with `--action` |
| `--action` | option | no | `request_passcode_resend` | Typed control to send instead of inputs; the only value is `request_passcode_resend` |
| `--expected-passcode-epoch` | option | no |  | Passcode epoch from the current notice; a stale or repeated epoch is refused before anything is clicked |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb task cancel`

Cancel a task and close its runtime; keeps the saved browser profile and returns the final state

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--task-ref` | option | no |  | Opaque reference of the task to cancel, as returned by `task start`; the command refuses to run without it |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb task result`

Print the final result of a finished task, including any places the website program does not cover; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--task-ref` | option | no |  | Opaque reference of the task, as returned by `task start`; the command refuses to run without it |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb task forget-answers`

Delete the answers an earlier version stored for this Portal user and site; needs the confirmation object on stdin and leaves browser profiles, drafts and saved logins alone

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input-stdin` | flag | no |  | Read the `{site, user_confirmed}` object from standard input |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb runtime doctor`

Check whether the signed local runtime for this package is installed and verified; changes nothing and reports `runtime_missing` when it is absent

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb runtime install`

Download the runtime declared by this package (matched by version, platform, size and checksum) and verify it; changes only the runtime cache

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb pair join`

Join with the code from Jacky's assistant after the person agrees

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--code` | option | yes |  | Join code given by Jacky's assistant |
| `--user-confirmed` | flag | no |  | Assert that the person agreed that Jacky's assistant may see this machine's AnyWeb status |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb pair status`

Show whether a connection is open

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb pair snapshot`

Send the current product envelope

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--envelope-json` | option | yes |  | JSON envelope file; `-` reads stdin |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb pair inbox`

Show unread instructions from Jacky's assistant

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb pair read`

Mark one instruction read after showing it

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--message-id` | option | yes |  | Id of the instruction that was shown |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb pair result`

Send the outcome of a requested action

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--ok` | flag | no |  | The requested action succeeded |
| `--error` | option | no |  | Short product reason when the action failed |
| `--envelope-json` | option | no |  | Optional JSON envelope file with the minimal status; `-` reads stdin |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb pair close`

End the connection

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--reason` | option | no |  | Short reason recorded with the close; defaults to `done` |
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb commands`

Print this tool's public command surface as JSON; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |

## `anyweb mcp serve`

Serve the MCP stdio tools (task status, resume and saved-login cache) until the host closes the connection; the tools never return passwords, codes or security answers

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted for scripts; output is always JSON |
