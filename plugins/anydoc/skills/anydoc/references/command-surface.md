<!-- generated-by: assert-skill-surface.py ; do not edit -->
# anydoc command reference

Generated from `anydoc commands --json` (version `0.3.19`). Every public command is listed.

## `anydoc doctor`

Report local health (install, PDF engine, Office converter, login presence) as JSON without login or network; with `--repair-install` it installs this package as the current version, removes older copies and reports what it removed

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |
| `--repair-install` | flag | no |  | Install the package this binary runs from as `plugins/anydoc/current`, delete every older version and stale copy, keep login and saved data, and bind the `anydoc` shim |

## `anydoc inspect`

Recursively inspect a client folder and return per-file facts (type, page count, text layer, protection, previews); writes only derived inspection files under `.anydoc-work`, never changes source files

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | no |  | Absolute path of the client folder to inspect |
| `--detail-level` | option | no |  | `summary` (default) returns compact facts; `full` is not available in this version |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc render-page`

Render one PDF page to a new PNG preview at higher resolution than the inspect preview; never overwrites an existing file or changes the source

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | no |  | Absolute path of the PDF to render |
| `--page` | option | no |  | One-based PDF page number |
| `--output` | option | no |  | New PNG path. Existing files are never overwritten |
| `--max-dimension` | option | no |  | Longest edge of the rendered PNG in pixels |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc extract-existing-text`

Return the text layer a PDF already contains for one page or all pages, never OCR; a scan with no text layer returns no text; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | no |  | Absolute path of the PDF to read |
| `--page` | option | no |  | One-based PDF page number |
| `--max-chars` | option | no |  | Maximum number of text characters returned |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc plan validate`

Validate a Plan v4 file against the inspected folder and report each problem with its exact field path; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--plan` | option | no |  | Absolute path of the Plan v4 JSON file |
| `--input` | option | no |  | Absolute path of the inspected client folder the plan refers to |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc plan show`

Show the validated plan as a human-reviewable preview (outputs, pages, warnings) and its approval hash; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--plan` | option | no |  | Absolute path of the Plan v4 JSON file |
| `--input` | option | no |  | Absolute path of the inspected client folder the plan refers to |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc plan approve`

Record the user's approval of exactly this plan by writing an approval receipt bound to its hash; run only after the user said yes, and any later change to the plan needs a new approval

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--plan` | option | no |  | Absolute path of the Plan v4 JSON file the user approved |
| `--receipt` | option | no |  | New approval receipt path to write |
| `--input` | option | no |  | Absolute path of the inspected client folder the plan refers to |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc assemble`

Execute an approved plan: write the planned copies, renames and PDF edits as a new immutable `anydoc-output/vNNN` snapshot under the input folder; refuses an unapproved or changed plan and never edits the sources

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | no |  | Absolute path of the inspected client folder |
| `--plan` | option | no |  | Absolute path of the Plan v4 JSON file that was approved |
| `--receipt` | option | no |  | Absolute path of the approval receipt written by `plan approve` |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc verify`

Mechanically check the written outputs against the approved plan and report each mismatch; read-only and never says a package is ready to submit

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | no |  | Absolute path of the inspected client folder |
| `--plan` | option | no |  | Absolute path of the Plan v4 JSON file that was approved |
| `--receipt` | option | no |  | Absolute path of the approval receipt written by `plan approve` |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc resume`

Resume an interrupted assemble from its journal and finish the same snapshot

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | no |  | Absolute path of the inspected client folder whose assemble was interrupted |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc clean`

Remove derived cache, host scratch, staging and journal files only; inspection, document map, plans, receipts, sources and every output snapshot are kept

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--input` | option | no |  | Absolute path of the client folder to clean |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc login`

Save the shared Portal token read from stdin into the local login slot (`token/user.json`) so saved models and Tell Jacky work; the token is never accepted on the command line or printed

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--token-stdin` | flag | no |  | Read the credential from stdin. Never accepted on argv |
| `--force` | flag | no |  | Overwrite an existing valid login instead of keeping it |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc logout`

Clear the local login slot; saved models on the server and local case files are untouched

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc whoami`

Check the saved login with the service and report a masked identity; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc guides list`

List the bundled assembly conventions, or with a pathway and stage the scoped public revisions of that Public Guide; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--pathway-id` | option | no |  | Public Guide pathway id; give together with `--stage` to list scoped public revisions |
| `--stage` | option | no |  | Public Guide stage; give together with `--pathway-id` |
| `--channel` | option | no |  | Release channel to list; `default` when omitted |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc guides show`

Show one bundled convention by id, or with a pathway and stage the current exact public revision of one Public Guide document list; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | no |  | Bundled convention id, or the Public Guide document-list id when scoped |
| `--pathway-id` | option | no |  | Public Guide pathway id; give together with `--stage` |
| `--stage` | option | no |  | Public Guide stage; give together with `--pathway-id` |
| `--channel` | option | no |  | Release channel; `default` when omitted |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc guides revision`

Fetch one Public Guide revision using its exact pin and owner scope; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--pathway-id` | option | no |  | Public Guide pathway id |
| `--stage` | option | no |  | Public Guide stage |
| `--document-list-id` | option | no |  | Document-list id of the guide |
| `--channel` | option | no |  | Release channel |
| `--program-release-id` | option | no |  | Pinned program release id |
| `--document-list-revision-id` | option | no |  | Pinned document-list revision id |
| `--payload-sha256` | option | no |  | Pinned SHA-256 of the revision payload |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc guides evaluate`

Evaluate one pinned Public Guide against a local facts file and return the comparison with its exact pin; local files never go to the service and nothing is written

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--pathway-id` | option | no |  | Public Guide pathway id |
| `--stage` | option | no |  | Public Guide stage |
| `--document-list-id` | option | no |  | Document-list id of the guide |
| `--channel` | option | no |  | Release channel |
| `--facts` | option | no |  | Absolute path of the local facts file |
| `--as-of` | option | no |  | Evaluation date (YYYY-MM-DD); required |
| `--jurisdiction` | option | no |  | Jurisdiction, when the pinned scope restricts it |
| `--locale` | option | no |  | Locale, when the pinned scope restricts it |
| `--program-release-id` | option | no |  | Pinned program release id |
| `--document-list-revision-id` | option | no |  | Pinned document-list revision id |
| `--payload-sha256` | option | no |  | Pinned SHA-256 of the revision payload |
| `--public-only-confirmed` | flag | no |  | The user chose public-only use without a private model |
| `--partial-scope-acknowledged` | flag | no |  | The user accepted a partial or incomplete evaluated scope |
| `--case-type` | option | no |  | Case type of an available saved private model to compare against |
| `--private-list-model` | option | no |  | Absolute path of an unsaved private list model to compare against |
| `--bindings` | option | no |  | Absolute path of the local bindings file for the private model |
| `--decisions` | option | no |  | Absolute path of the local file with dispositions for listed differences |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc models list`

List the user's saved private assembly models (id, case type, title, revision); read-only, needs login

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc models show`

Show one saved model with its rules and revision; read-only, needs login

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | no |  | Saved model id |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc models resolve`

Find the saved model for an exact case type and return `model_absent` when none exists; read-only, needs login

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--case-type` | option | no |  | Exact case type to resolve |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc models validate`

Check a model draft file locally and return `valid` plus its `draft_sha256`; nothing is saved or sent

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--model` | option | no |  | Absolute path of the typed model draft JSON file |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc models save`

Save a new private model on the account after the user confirmed it; creates a record, needs login

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--model` | option | no |  | Absolute path of the typed model draft JSON file |
| `--idempotency-key` | option | no |  | Key that makes a retried save create only one record |
| `--user-confirmed` | flag | no |  | The user confirmed this exact model; required |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc models replace`

Replace the rules of a saved model with a new draft after the user confirmed it; creates a new revision and is refused when the revision is stale, needs login

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | no |  | Saved model id |
| `--expected-revision` | option | no |  | Revision the draft was based on |
| `--model` | option | no |  | Absolute path of the typed model draft JSON file |
| `--user-confirmed` | flag | no |  | The user confirmed this exact change; required |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc models forget`

Delete a saved model from the account after the user confirmed it; refused when the revision is stale, needs login

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | no |  | Saved model id |
| `--expected-revision` | option | no |  | Revision the user last saw |
| `--user-confirmed` | flag | no |  | The user confirmed the deletion; required |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc models export`

Write a saved model's full record to a new local file for editing; read-only on the account, needs login

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | no |  | Saved model id |
| `--output` | option | no |  | New file path to write |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc photos`

Not in this version; always returns not_implemented

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc office-preview`

Not in this version; always returns not_implemented

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc feedback create`

Send one bug report, feature request or tip to Jacky after the user confirmed the exact draft; creates a record, needs login

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--type` | option | no |  | Kind: `bug-report`, `feature-request` or `knowledge-tip` |
| `--title` | option | no |  | One-line title shown to the user before sending |
| `--description` | option | no |  | Plain-language description shown to the user before sending |
| `--user-confirmed` | flag | no |  | The user confirmed this exact draft; required |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc feedback list`

List the user's own feedback items; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc feedback status`

Show the status of one feedback item; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--id` | option | no |  | Feedback item id |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc feedback inbox`

Show unread replies to the user's feedback; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc feedback read`

Mark one reply read after the user has seen it

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--update-id` | option | no |  | Id of the reply to mark read |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc pair join`

Join with the code from Jacky's assistant after the person agrees

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--code` | option | yes |  | Join code given by Jacky's assistant |
| `--user-confirmed` | flag | no |  | The person agreed to the connection; required |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc pair status`

Show whether a connection is open

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc pair snapshot`

Send the current product envelope

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--envelope-json` | option | yes |  | JSON envelope file; `-` reads stdin |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc pair inbox`

Show unread instructions from Jacky's assistant

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc pair read`

Mark one instruction read after showing it

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--message-id` | option | yes |  | Id of the instruction to mark read |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc pair result`

Send the outcome of a requested action

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--ok` | flag | no |  | The requested action succeeded |
| `--error` | option | no |  | Short plain-language failure reason when the action failed |
| `--envelope-json` | option | no |  | Optional JSON envelope file with the result; `-` reads stdin |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc pair close`

End the connection

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--reason` | option | no |  | Short reason recorded when the connection ends |
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc guide-feedback`

Not in this version; always returns not_implemented

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted and ignored: stdout is always JSON |

## `anydoc commands`

Dump the public CLI command surface (every command, argument and summary) as JSON; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Always JSON; accepted so `anydoc commands --json` is the contract |
