<!-- generated-by: assert-skill-surface.py ; do not edit -->
# anypdf command reference

Generated from `anypdf commands --json` (version `0.7.37`). Every public command is listed.

## Global options

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted on every command except `read`; output is JSON either way. |

## `anypdf login`

Save your access key on this machine after proving it works; returns who you are signed in as and changes only the saved key.

The key must be piped on stdin; it is never accepted as an argument, and a key that was not verified against the service is not saved.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--token-stdin` | flag | no |  | Read the access key from standard input. |

## `anypdf whoami`

Show which account this machine is signed in as (masked identity, role, scopes, form access and expiry); changes nothing.

No arguments.

## `anypdf logout`

Remove the saved access key from this machine; returns a confirmation and contacts no service.

No arguments.

## `anypdf doctor`

Check that this machine is set up correctly, offline: returns the client version, whether a key is saved and the state of the installed copy; changes nothing unless `--repair-install` is given.

`--repair-install` installs the package this binary belongs to as the only copy under the install home, switches `current` to it, deletes the previous copy, older plugin trees and older host-cache versions, and keeps login and settings. It needs no login and prints removed, leftover and pending_cleanup lists; a non-empty leftover list makes it fail.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--repair-install` | flag | no |  | Install this package as the only live copy and delete everything older. |

## `anypdf read`

Turn a local Office or PDF file into Markdown on this machine; prints Markdown (not JSON), uploads nothing and refuses photos, screenshots and scanned pages.

`--mode content` keeps meaningful filled facts without list-of-values catalogs or empty widgets; `--mode structure` dumps the mechanical PDF field tree for fill work. `--json` is refused here.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | yes |  | Absolute path of the file to read. |
| `--mode` | option | no | `content`, `structure` | What to extract; defaults to `content`. |

## `anypdf forms catalog`

List the forms available to you; returns the published catalog entries and changes nothing.

Ask this live; the list changes between releases and is never copied into the skill.

No arguments.

## `anypdf forms resolve`

Find a form by name or number; returns the matching candidates without choosing one for you and changes nothing.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--query` | option | yes |  | Form name or number to look for, for example `IMM 5257`. |

## `anypdf forms schema`

Show what a form asks for; returns its answer schema with schema version and filling guidance, and changes nothing.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--form-id` | option | yes |  | Form id from `forms catalog` or `forms resolve`. |
| `--version` | option | no |  | Form version; defaults to the current published version. |
| `--locale` | option | no |  | Locale of the form text to use. |

## `anypdf validate`

Check answers against a form's rules before filling anything; returns errors, warnings and notes, creates no fill job and changes nothing.

With `--resolve-choice` it also returns the certified values for that choice field.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--form-id` | option | yes |  | Form id from `forms catalog` or `forms resolve`. |
| `--input` | option | yes |  | Path to the JSON file holding the answers, shaped by the form's schema. |
| `--schema-version` | option | no |  | Schema version the answers were written for, as returned by `forms schema`. |
| `--version` | option | no |  | Form version; defaults to the current published version. |
| `--locale` | option | no |  | Locale of the form text to use. |
| `--resolve-choice` | option | no |  | Field path whose certified choice values should be returned; may be repeated. |

## `anypdf complete start`

Check answers and, when something is missing, return a short form to fill in (in chat or in your browser on this machine); starts a completion session and fills no PDF.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--form-id` | option | yes |  | Form id from `forms catalog` or `forms resolve`. |
| `--input` | option | yes |  | Path to the JSON file holding the answers, shaped by the form's schema. |
| `--schema-version` | option | no |  | Schema version the answers were written for, as returned by `forms schema`. |
| `--version` | option | no |  | Form version; defaults to the current published version. |
| `--locale` | option | no |  | Locale of the form text to use. |

## `anypdf complete apply`

Hand back the values typed into the in-chat form; returns the merged answers for the session and fills no PDF.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--session-id` | option | yes |  | Session id returned when the session was started. |
| `--values` | option | yes |  | Path to the JSON file holding the values typed into the form. |

## `anypdf complete result`

Collect the completed answers sent from the browser page; returns them (or the still-pending state) and fills no PDF.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--session-id` | option | yes |  | Session id returned when the session was started. |
| `--wait` | option | no |  | Seconds to wait for the page to be submitted before returning. |

## `anypdf fill submit`

Ask for a filled PDF; creates one fill job (repeating the same idempotency key creates nothing new) and returns its job id or the reasons it cannot be filled yet.

By default the job produces the interactive filled PDF. `--flatten` also asks for a static viewing PDF; it is not for IRCC upload and does not replace the interactive PDF. To fill with some answers intentionally missing, add `--allow-incomplete` together with either the `--validation-id`, `--warnings-hash`, `--data-hash` and `--intentionally-missing` values from `validate`, or one `--confirmation-handle`.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--form-id` | option | yes |  | Form id from `forms catalog` or `forms resolve`. |
| `--input` | option | yes |  | Path to the JSON file holding the answers, shaped by the form's schema. |
| `--idempotency-key` | option | yes |  | Stable key for this request; reuse the same key when retrying so nothing is created twice. |
| `--schema-version` | option | no |  | Schema version the answers were written for, as returned by `forms schema`. |
| `--version` | option | no |  | Form version; defaults to the current published version. |
| `--locale` | option | no |  | Locale of the form text to use. |
| `--flatten` | flag | no |  | Also produce a static viewing PDF so Preview or a browser can show the pages. |
| `--allow-incomplete` | flag | no |  | Fill although some answers are intentionally missing; needs the confirmation values below. |
| `--validation-id` | option | no |  | Validation id from `validate`; used with `--allow-incomplete`. |
| `--warnings-hash` | option | no |  | Warnings hash from `validate`; used with `--allow-incomplete`. |
| `--data-hash` | option | no |  | Data hash from `validate`; used with `--allow-incomplete`. |
| `--intentionally-missing` | option | no |  | Field path the user chose to leave blank; may be repeated. |
| `--confirmation-handle` | option | no |  | Confirmation handle returned for the blank fields; used with `--allow-incomplete`. |

## `anypdf fill readiness`

Check whether answers can be filled right now; returns the readiness assessment and creates no job.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--form-id` | option | yes |  | Form id from `forms catalog` or `forms resolve`. |
| `--input` | option | yes |  | Path to the JSON file holding the answers, shaped by the form's schema. |
| `--schema-version` | option | no |  | Schema version the answers were written for, as returned by `forms schema`. |
| `--version` | option | no |  | Form version; defaults to the current published version. |
| `--locale` | option | no |  | Locale of the form text to use. |
| `--payment-source` | option | no |  | Payment source to assess the fill against; defaults to `auto`. |
| `--resolve-choice` | option | no |  | Field path whose certified choice values should be returned; may be repeated. |

## `anypdf fill status`

Check whether a fill is ready; returns the job state and changes nothing.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--job-id` | option | yes |  | Fill job id returned by `fill submit`. |

## `anypdf fill download`

Save the finished PDF of a completed fill to a file on this machine; returns the saved path, size and checksum.

The default is the interactive filled PDF; `--flatten` saves the extra static viewing copy instead.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--job-id` | option | yes |  | Fill job id returned by `fill submit`. |
| `--output` | option | yes |  | Absolute path of the file to write. |
| `--flatten` | flag | no |  | Save the static viewing copy instead of the interactive filled PDF. |

## `anypdf signatures create`

Start signing a completed IMM5476 fill; creates a signing session and returns its id without signing anything.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--job-id` | option | yes |  | Fill job id returned by `fill submit`. |
| `--input` | option | yes |  | Path to the JSON file describing the signers and signing context. |

## `anypdf signatures status`

Check a signing session; returns its current version, signers and assets and changes nothing.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--session-id` | option | yes |  | Session id returned when the session was started. |

## `anypdf signatures add-asset`

Upload a signature image into a signing session; returns the asset id and signs nothing.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--session-id` | option | yes |  | Session id returned when the session was started. |
| `--image` | option | yes |  | Absolute path of the signature image. |

## `anypdf signatures assign`

Match an uploaded image to its signer and date; returns the updated session and signs nothing.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--session-id` | option | yes |  | Session id returned when the session was started. |
| `--input` | option | yes |  | Path to the JSON file assigning each image to a signer and date. |

## `anypdf signatures preview`

Review every signer, image and date before signing; returns the preview the human must approve and signs nothing.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--session-id` | option | yes |  | Session id returned when the session was started. |
| `--expected-version` | option | yes |  | Session version the preview must match, as returned by `signatures status`. |

## `anypdf signatures confirm`

Sign the session after the human approved the preview; returns the signing result and cannot be undone.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--session-id` | option | yes |  | Session id returned when the session was started. |
| `--input` | option | yes |  | Path to the JSON file carrying the approved preview confirmation. |
| `--user-confirmed` | flag | no |  | Assert that the human approved the preview just reviewed. |

## `anypdf signatures download`

Save the signed, static page-image PDF to a file on this machine; returns the saved path.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--session-id` | option | yes |  | Session id returned when the session was started. |
| `--output` | option | yes |  | Absolute path of the file to write. |

## `anypdf intake submit`

Send an issuing-authority official blank PDF so a missing form can be recognised; uploads that file once and returns known_exact, new_source or rejected.

Only the issuing authority's official blank template is accepted. `new_source` does not mean the form is published or ready to fill, and the command is its own confirmation: there is no status or confirm step.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--pdf` | option | yes |  | Path of the official blank PDF. |
| `--idempotency-key` | option | yes |  | Stable key for this request; reuse the same key when retrying so nothing is created twice. |

## `anypdf feedback submit`

Send a note to the product owner (Tell Jacky); creates one report and returns its report id, and carries no PDF bytes.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--report` | option | yes |  | Path to the JSON file holding the report. |
| `--idempotency-key` | option | no |  | Stable key so a retried send creates one report only. |

## `anypdf feedback status`

Check on a note you sent; returns its status and changes nothing.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--report-id` | option | yes |  | Report id returned by `feedback submit`. |

## `anypdf knowledge list`

Show the filling rules you saved; returns them (global, or for one form) and changes nothing.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--scope` | option | no | `target`, `global` | Which rules to list: for one form (`target`) or for every form (`global`). |
| `--form-id` | option | no |  | Limit the result to one form id. |

## `anypdf knowledge add`

Save a rule to apply when filling; stores one rule and returns the saved rule.

Use `--origin agent_proposed` only for text the agent proposed and the user approved word for word, and `--supersedes-id` to replace an older rule after a separate confirmation.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--rule` | option | yes |  | The rule text to save. |
| `--scope` | option | no | `target`, `global` | Apply the rule to one form (`target`) or to every form (`global`). |
| `--form-id` | option | no |  | Limit the result to one form id. |
| `--origin` | option | no | `user_authored`, `agent_proposed` | Who wrote the rule text. |
| `--supersedes-id` | option | no |  | Id of the saved rule this one replaces. |

## `anypdf knowledge remove`

Delete a saved filling rule; returns a confirmation and cannot be undone.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | yes |  | Id of the saved rule to delete. |

## `anypdf resume`

Continue the exact saved request behind a needs-agent response; reads that whole JSON response on stdin and returns the original command's result.

Never rebuild the original arguments: the client resumes its sealed request, including after a login and from another directory.

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--envelope-stdin` | flag | yes |  | Read the needs-agent response from standard input. |

## `anypdf commands`

Print the public command surface of this client as JSON; changes nothing.

No arguments.
