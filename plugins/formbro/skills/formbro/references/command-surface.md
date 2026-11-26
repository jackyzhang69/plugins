<!-- generated-by: assert-skill-surface.py ; do not edit -->
# formbro command reference

Generated from `formbro commands --json` (version `1.9.14`). Every public command is listed.

## Global options

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--base-url` | option | no |  | Backend URL to use for this call instead of the saved one |

## `formbro login`

Save the FormBro API token read from stdin so later commands are signed in; returns the saved paths and changes local settings only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--token-stdin` | flag | no |  | Read exactly one token line from stdin (terminal input is hidden); never pass the token as an argument |
| `--base-url` | option | no |  | Backend URL to save with the token; defaults to the production backend |

## `formbro health`

Check that the FormBro backend is reachable; returns its health status and changes nothing

No arguments.

## `formbro whoami`

Show which FormBro account the saved token belongs to; returns the account details and changes nothing

No arguments.

## `formbro programs list`

List every program key with its category and endpoint; changes nothing

No arguments.

## `formbro programs describe`

Show one program's details, such as its category, roles and endpoints; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<PROGRAM_KEY>` | positional | yes |  | Program key from `programs list` |

## `formbro programs schema`

Show the data schema of one role in a program; use it to learn field names before writing data; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<PROGRAM_KEY>` | positional | yes |  | Program key from `programs list` |
| `--role` | option | yes |  | Role whose schema to show, such as applicant or employer |

## `formbro find`

Search people, applications and employers by free text; returns matching records and changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<QUERY>` | positional | yes |  | Free text to search for, such as a name, email or company |
| `--program-key` | option | no |  | Limit results to one program key |
| `--include` | option | no |  | Comma-separated record kinds to include in the results |
| `--limit` | option | no |  | Maximum number of results |

## `formbro applicants list`

List applicants, optionally filtered by search text or program; returns summaries and changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--search` | option | no |  | Free text matched against applicant names and emails |
| `--program-key` | option | no |  | Limit results to one program key |
| `--limit` | option | no |  | Maximum number of results |

## `formbro applicants get`

Read one applicant's full record; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<ID>` | positional | yes |  | Applicant id |

## `formbro applicants patch`

Change fields on an applicant and return the updated record

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<ID>` | positional | yes |  | Applicant id |
| `--program-key` | option | no |  | Program key the person belongs to |
| `--data` | option | no |  | JSON object of fields to change |
| `--set` | option | no |  | One field change as `path=value`; repeat the flag for several |
| `--expected-version` | option | no |  | Version number from the last read; the change is refused if the record has changed since |

## `formbro applicants delete`

Permanently delete one applicant and return the backend's answer

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<ID>` | positional | yes |  | Applicant id |

## `formbro applications list`

List the account's active applications (the dashboard view); returns summaries and changes nothing. Use `inventory` to include every status

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--status` | option | no |  | Only applications in this status |
| `--program-key` | option | no |  | Limit results to one program key |
| `--limit` | option | no |  | Maximum number of results |
| `--offset` | option | no |  | Number of results to skip, for paging |

## `formbro applications by-status`

List applications that are in one status, optionally for a minimum number of days; returns summaries and changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<STATUS>` | positional | yes |  | Status to look for |
| `--program-key` | option | no |  | Limit results to one program key |
| `--days-in-status` | option | no |  | Only applications that have been in the status at least this many days |
| `--limit` | option | no |  | Maximum number of results |

## `formbro applications get`

Read one application's full record; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<ID>` | positional | yes |  | Application id |

## `formbro applications status`

Show one application's current status; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<ID>` | positional | yes |  | Application id |

## `formbro applications persons`

List the people attached to one application with their roles; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<ID>` | positional | yes |  | Application id |

## `formbro applications documents`

List the documents of one application; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<ID>` | positional | yes |  | Application id |

## `formbro applications start`

Create a new application for a program, optionally for an existing applicant, and return it

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--program-key` | option | no |  | Program key of the new application |
| `--applicant-id` | option | no |  | Existing applicant to link as the main applicant |

## `formbro applications attach`

Attach an existing person to an application in a role and return the updated application

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--app-id` | option | yes |  | Application id |
| `--role` | option | yes |  | Role the person takes in the application |
| `--person-id` | option | yes |  | Id of the person to attach |

## `formbro applications replace-person`

Replace the person holding a role in an application with another person

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--app-id` | option | yes |  | Application id |
| `--role` | option | yes |  | Role whose holder is replaced |
| `--person-id` | option | yes |  | Id of the new person |

## `formbro applications remove-person`

Remove a person from a role in an application

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--app-id` | option | yes |  | Application id |
| `--role` | option | yes |  | Role to remove the person from |
| `--person-id` | option | yes |  | Id of the person to remove |

## `formbro applications set-status`

Set an application's status; use the exact status name the backend accepts

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--app-id` | option | yes |  | Application id |
| `--program-key` | option | no |  | Program key of the application |
| `--status` | option | yes |  | New status name |
| `--expected-version` | option | no |  | Version number from the last read; the change is refused if the record has changed since |

## `formbro applications patch`

Change fields on an application, including `case.rcic_id` (the linked RCIC profile), and return the result

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<ID>` | positional | yes |  | Application id |
| `--program-key` | option | no |  | Program key of the application |
| `--data` | option | no |  | JSON object of fields to change |
| `--set` | option | no |  | One field change as `path=value`, for example `case.rcic_id=<rcic-id>`; repeat the flag for several |
| `--expected-version` | option | no |  | Version number from the last read; the change is refused if the record has changed since |

## `formbro applications resolve`

Resolve a free-text query (e.g. applicant name) to an application id within a given program. Returns structured JSON: unique / ambiguous (with candidates[]) / none. Agent surface — use this when the user names a person, not an id

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--query` | option | yes |  | Free-text search query (applicant name, email, partial match all OK) |
| `--program-key` | option | yes |  | Required program-key filter. Without this, name collisions across programs are unresolvable |
| `--limit` | option | no |  | Cap on candidates returned when query is ambiguous |

## `formbro applications inventory`

List EVERY application visible to the auth token (any status, incl. empty / archived / draft-with-bad-status). Use this for audit sweeps, batch test runs, or when `applications list` returns empty unexpectedly. Backed by /api/mcp/find wildcard search; flattens person→applications. Semantic note: `applications list` filters by dashboard ACTIVE statuses only; `inventory` returns everything. See formbro-read SKILL.md

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--program-key` | option | no |  | Limit results to one program key; omit for all programs |
| `--limit` | option | no |  | Cap on results |
| `--query` | option | no |  | Free-text name filter. Default `*` matches any |

## `formbro employers list`

List LMIA employers; returns summaries and changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--search` | option | no |  | Free text matched against employer names |
| `--stream` | option | no |  | LMIA stream to list, such as hws |
| `--limit` | option | no |  | Maximum number of results |

## `formbro employers get`

Read one employer's full record; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<ID>` | positional | yes |  | Employer id |

## `formbro employers create`

Create an LMIA employer and return it

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--company-name` | option | yes |  | Employer company name |
| `--business-number` | option | no |  | Canada Revenue Agency business number |
| `--contact-first-name` | option | no |  | Contact person's first name |
| `--contact-last-name` | option | no |  | Contact person's last name |
| `--contact-email` | option | no |  | Contact email address |
| `--contact-phone` | option | no |  | Contact phone number |
| `--contact-position` | option | no |  | Contact person's position |

## `formbro employers patch`

Change fields on an employer from a JSON object and return the result

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<ID>` | positional | yes |  | Employer id |
| `--data` | option | yes |  | JSON object of fields to change |

## `formbro employers delete`

Permanently delete one employer and return the backend's answer

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<ID>` | positional | yes |  | Employer id |

## `formbro validate data`

Validate raw JSON against an entity-type's schema (no DB lookup)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--entity-type` | option | yes |  | Entity type slug, such as `tr-sp-in-applicant` |
| `--data` | option | yes |  | JSON object to validate |
| `--tab` | option | no |  | Limit validation to one form tab |

## `formbro validate by-id`

Validate a stored entity by id. entity-type format: <category-lc>-<program-key>-<role>

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--entity-type` | option | yes |  | Entity type slug, such as `tr-sp-in-applicant` |
| `--entity-id` | option | yes |  | Id of the stored entity |
| `--tab` | option | no |  | Limit validation to one form tab |

## `formbro validate person`

Validate a person for a specific program + role (program-aware checks)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--person-id` | option | yes |  | Id of the person to validate |
| `--program-key` | option | no |  | Program key to validate against |
| `--role` | option | no |  | Role the person holds in the program |

## `formbro validate operation`

Validate that an operation (e.g. submit, attach) is allowed in the entity's current state

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--operation` | option | yes |  | Operation to check, such as submit or attach |
| `--entity-type` | option | yes |  | Entity type slug, such as `tr-sp-in-applicant` |
| `--entity-id` | option | no |  | Id of a stored entity to check the operation on |
| `--entity-data` | option | no |  | JSON object to check the operation on, instead of a stored entity |

## `formbro extract contract`

Fetch the prompt, schema and save instructions an agent needs to write JSON for an existing entity; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--program-key` | option | no |  | Program key of the entity |
| `--entity-type` | option | yes |  | Entity type slug, such as `tr-sp-in-applicant` |
| `--sub-object-type` | option | no |  | Sub-object of the entity to extract into |

## `formbro extract apply-json`

Validate agent-written JSON and save it onto an existing applicant, application or employer

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--target-entity-id` | option | yes |  | Id of the entity to update |
| `--target-entity-type` | option | yes | `applicant`, `application`, `employer` | Kind of entity to update |
| `--program-key` | option | no |  | Program key of the entity |
| `--json` | option | yes |  | Agent-written JSON to validate and save |
| `--sub-object-type` | option | no |  | Sub-object of the entity to write into |
| `--expected-version` | option | no |  | Version number from the last read; the save is refused if the record has changed since |

## `formbro extract text`

Run FormBro's server-side extraction on pasted text and return the proposed data

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--text` | option | yes |  | Text to extract data from |
| `--program-key` | option | no |  | Program key of the entity |
| `--entity-type` | option | yes |  | Entity type slug, such as `tr-sp-in-applicant` |
| `--entity-id` | option | no |  | Id of an existing entity to extract for |
| `--sub-object-type` | option | no |  | Sub-object of the entity to extract into |
| `--model` | option | no |  | Extraction model name from `extract models` |

## `formbro extract task-status`

Check the progress or result of an extraction task by its id from `extract text`

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<TASK_ID>` | positional | yes |  | Task id returned by `extract text` |

## `formbro extract models`

List the extraction models the backend offers; changes nothing

No arguments.

## `formbro extract formats`

List the input file formats extraction supports; changes nothing

No arguments.

## `formbro extract clear-cache`

Clear the backend's extraction cache; changes no case data

No arguments.

## `formbro import contract`

Return prompt + schema + save contract for a local agent to generate JSON

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--program-key` | option | no |  | Program key of the new case |
| `--target` | option | no |  | What the import creates; the default is a new case |

## `formbro import apply-json`

Validate and optionally save local-agent generated import JSON

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--program-key` | option | no |  | Program key of the new case |
| `--target` | option | no |  | What the import creates; the default is a new case |
| `--json` | option | yes |  | Agent-written import JSON |
| `--dry-run` | flag | no |  | Validate only and save nothing |
| `--allow-incomplete` | flag | no |  | Save the case even when required information is still missing; the result lists what is missing |

## `formbro export entity`

Export one saved entity, named by entity type and id, as an Excel file; works for entity types the backend supports, such as saved General PR / Express Entry applications

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--entity-type` | option | yes |  | Kind of entity to export |
| `--entity-id` | option | yes |  | Id of the entity |
| `--language` | option | no |  | Output language code |
| `--program-key` | option | no |  | Program key of the entity |
| `--blank` | flag | no |  | Export an empty template instead of the entity's data |
| `--output` | option | yes |  | Output file path |

## `formbro export data`

Turn JSON form data you provide into an Excel file for one form id; nothing is saved

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--form-id` | option | yes |  | IMM form id, such as 5257 |
| `--data` | option | yes |  | JSON object holding the form data |
| `--program-key` | option | no |  | Program key of the form |
| `--language` | option | no |  | Output language code |
| `--output` | option | yes |  | Output file path |

## `formbro export template`

Download a blank Excel template for one form id

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--form-id` | option | yes |  | IMM form id, such as 5257 |
| `--language` | option | no |  | Output language code |
| `--output` | option | yes |  | Output file path |

## `formbro export pdf`

Render selected IMM PDFs for an application synchronously. TR full / PR subset / LMIA unsupported

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--program-key` | option | yes |  | Program key of the application |
| `--app-id` | option | yes |  | Application id |
| `--forms` | option | no |  | Comma-separated IMM form ids to render |
| `--output` | option | no |  | Output path for the rendered PDFs |

## `formbro export pdf-check`

Probe which forms are supported for a program without actually rendering

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--program-key` | option | yes |  | Program key of the application |
| `--app-id` | option | yes |  | Application id |
| `--forms` | option | no |  | Comma-separated IMM form ids to render |
| `--output` | option | no |  | Output path for the rendered PDFs |

## `formbro export extension`

Export the application bundle for the FormBro browser extension

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--program-key` | option | yes |  | Program key of the application |
| `--app-id` | option | yes |  | Application id |

## `formbro persons create`

Create a new person (or employer contact) for a program and return it

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--program-key` | option | no |  | Program key the person belongs to |
| `--role` | option | yes |  | Role of the new person, such as applicant, spouse or employer |
| `--first-name` | option | no |  | First name |
| `--last-name` | option | no |  | Last name |
| `--email` | option | no |  | Email address |
| `--company-name` | option | no |  | Company name, for company roles |

## `formbro persons patch`

Change fields on a person and return the updated record

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<ID>` | positional | yes |  | Applicant id |
| `--program-key` | option | no |  | Program key the person belongs to |
| `--data` | option | no |  | JSON object of fields to change |
| `--set` | option | no |  | One field change as `path=value`; repeat the flag for several |
| `--expected-version` | option | no |  | Version number from the last read; the change is refused if the record has changed since |

## `formbro rcic list`

List the account's RCIC profiles with their saved portal credentials (usernames, passwords, security Q&As) for logging in to IRCC portals. The listed `id` is the value backend validation expects for `case.rcic_id` on an application (e.g. PR webform fills)

No arguments.

## `formbro uploads slots`

List the document upload slots of one record; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--entity-type` | option | yes |  | Kind of record, such as applicant or application |
| `--entity-id` | option | yes |  | Id of the record |

## `formbro webform runtime-check`

Verify Playwright + Chromium are usable on this machine. Run once before first `start`

No arguments.

## `formbro webform preflight`

Check whether the case has all required fields for a webform fill (no browser opened)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--app-id` | option | yes |  | Application id |
| `--program-key` | option | no |  | Program key of the application |

## `formbro webform start`

Open a local browser and fill the IRCC/Service Canada portal. Stops at "Ready to submit". Accepts EITHER `--app-id` OR `--query` (name to resolve). Internal flow: (1) resolve to app_id if --query, (2) fetch application + linked applicant/RCIC ids, (3) PARALLEL validate application + applicant + RCIC against the fill_webform operation, (4) preflight, (5) compute-actions, (6) worker fill. Any layer 1-4 failure returns a structured envelope and DOES NOT spawn the browser. Requires --confirmed

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--app-id` | option | no |  | Existing application id. Mutex with --query; exactly one of the two is required |
| `--query` | option | no |  | Free-text query (applicant name, email, partial OK) resolved to a unique application id within --program-key. Mutex with --app-id; exactly one is required. If multiple matches, the command returns a structured candidates list and exits |
| `--case-id` | option | no |  | IRCC portal case id (6 or 7 digits). PR: continue that case — all forms, or one form with --form. PR Renewal: resume that renewal case |
| `--form` | option | no | `0008`, `5406`, `5669`, `5562` | PR non-renewal only: update ONE form inside an existing IRCC PR Portal case instead of re-filling all forms. Requires --case-id |
| `--new` | flag | no |  | PR only: create a NEW portal application even when this application is already linked to a portal case (the client receives another IRCC email). Default: continue the linked case |
| `--program-key` | option | yes |  | program-key (e.g. general, express-entry, renewal, sp-out). REQUIRED: drives query collision-resolution AND backend routing for both --app-id and --query modes |
| `--confirmed` | flag | no |  | Required. Confirms the user has approved the fill; the browser is not opened without it |
| `--headless` | option | no | `true`, `false` | Default false: user-initiated portal fills should remain visible unless background execution is explicitly requested |
| `--print-pages` | option | no | `true`, `false` | Page Record: capture every portal page to PDF during the fill and merge them (in fill order) into one downloadable record PDF. Default TRUE for the plugin; pass `--print-pages false` to disable. Records land in ~/.formbro/records/ (30-day retention, pruned on worker start/fill) |
| `--debug-events` | flag | no |  | Emit the full WebFiller event trace in the response JSON. Default off (compact summary only). Useful for debugging a fill that didn't finish |

## `formbro webform jobbank-invite`

Open a local browser and invite qualified candidates from a Job Bank posting. Uses the bundled worker on this machine, not the backend's server-side Playwright path. Requires --confirmed because this action sends live invitations

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--job-post-id` | option | yes |  | Job Bank posting id |
| `--rcic-id` | option | yes |  | RCIC profile id from `rcic list` |
| `--minimum-stars` | option | no |  | Minimum star rating a candidate needs to be invited |
| `--items-per-page` | option | no |  | Candidates listed per page |
| `--max-invites` | option | no |  | Maximum number of invitations to send |
| `--confirmed` | flag | no |  | Required. Confirms the user has approved sending live invitations |
| `--headless` | option | no | `true`, `false` | Default false: this flow often needs a visible browser if Job Bank shows Terms of Use or another manual gate |
| `--debug-events` | flag | no |  | Emit the full event trace in the response JSON |

## `formbro webform status`

Read the BACKEND-PERSISTED status of the last fill run on this app. May be stale in local mode — see `webform-cap`

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--app-id` | option | yes |  | Application id |
| `--program-key` | option | no |  | Program key of the application |

## `formbro webform daemon start`

Ensure the worker daemon is running (spawns if not). Prints its socket

No arguments.

## `formbro webform daemon stop`

Gracefully stop the worker daemon (no-op if not running)

No arguments.

## `formbro webform daemon status`

Print health + uptime of the running daemon, or {"running": false}

No arguments.

## `formbro webform daemon restart`

Stop then start the worker daemon

No arguments.

## `formbro webform daemon prune-chromium`

Delete Chromium revisions other than the pinned one. Frees disk

No arguments.

## `formbro notes add`

Append a timestamped note to an applicant, application or employer and return the updated record

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--entity-id` | option | yes |  | Id of the record to annotate |
| `--entity-type` | option | yes |  | Kind of record: applicant, application or employer |
| `--program-key` | option | no |  | Program key; needed for applications |
| `--note` | option | yes |  | Note text to append |

## `formbro feedback create`

Send a feature request, bug report or knowledge tip to FormBro and return the created entry

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--type` | option | yes | `feature-request`, `bug-report`, `knowledge-tip` | Kind of feedback |
| `--title` | option | yes |  | Short title |
| `--description` | option | yes |  | Full description of the problem, idea or tip |
| `--application-id` | option | no |  | Exact FormBro application ID. Required for bug reports |
| `--url` | option | no |  | Relevant page/portal URL. Merged into context.page_url |
| `--context-json` | option | no |  | Extra context fields as a raw JSON object (e.g. program_key, entity_type, error_message). Merged under `context`; `source` is always stamped as "formbro-cli" regardless of this value |
| `--image` | option | no |  | Local screenshot/image path. Repeatable; each is attached after create |

## `formbro audit my`

List the account's own audit entries, newest first; changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--from` | option | no |  | Only entries on or after this date |
| `--to` | option | no |  | Only entries on or before this date |
| `--action` | option | no |  | Only entries for this action name |
| `--limit` | option | no |  | Maximum number of entries |

## `formbro pdf bundle`

Stage a new IRCC PDF: extract orig_oid + create vendor metadata files

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<PDF_PATH>` | positional | yes |  | Path to source IRCC PDF (must include form id in filename, e.g. 5257.pdf) |
| `--vendor-root` | option | no |  | Vendor root directory (default: backend/vendor/pdffiller) |

## `formbro pdf verify`

Verify all 15 IRCC forms have required vendor files (orig_oid, form_, etc.)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--vendor-root` | option | no |  | Vendor root directory (default: backend/vendor/pdffiller) |

## `formbro fill`

Fill IRCC IMM PDF forms for an application. Agent-facing PDF entry point. Auto-detects TR vs PR from the application; rejects LMIA (use `webform start`). The backend renders the PDFs and the CLI writes them into the chosen directory

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--app-id` | option | yes |  | Application id |
| `--forms` | option | no |  | Comma-separated form list, with or without IMM prefix (IMM5257 == 5257) |
| `--program-key` | option | no |  | Optional override; auto-detected from application when omitted |
| `--output` | option | no |  | Output directory. Created if missing |

## `formbro doctor`

Report this install's version, runtime readiness and a live backend check as JSON; `--repair-install` instead reinstalls this package as the only installed copy

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--fetch` | flag | no |  | Pre-fetch the lazy webform-worker runtime now (instead of on first `webform start`). Implies network access + writes under `~/Library/Caches/formbro-cli` (or platform equivalent) |
| `--no-fetch` | flag | no |  | Skip the bootstrap step entirely — only report binary version + cache freshness. No network access, no filesystem writes outside the parse of binary path metadata. This is the form agents should call at session start |
| `--json` | flag | no |  | Emit machine-readable JSON instead of human prose. Agents should call `formbro doctor --json --no-fetch` at session start to detect a stale codex / claude plugin cache without triggering network IO |
| `--check-upgrade` | flag | no |  | Check the plugin marketplace for a newer version. Reads the Codex marketplace manifest first, then falls back to repository tags. Failure is non-fatal — the rest of doctor output is unchanged. Agents should call this from connect-formbro to surface upgrade hints early (avoids users hitting already-fixed bugs). Compatible with --no-fetch: that flag only skips local bootstrap; --check-upgrade is an independent network call |
| `--repair-install` | flag | no |  | Reinstall this package as the only installed copy: switch `~/.jackyzhang.app/plugins/formbro/current` to it, then delete every other tree, the old bootstrap caches and older versions in the host plugin cache. Login, settings and user data are kept. Run it with the binary from the newly installed package. Prints JSON; works offline and without a login |

## `formbro commands`

Dump the public CLI command surface as JSON (plugin skill contract)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Always JSON; accepted so `formbro commands --json` is the contract |
