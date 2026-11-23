<!-- generated-by: assert-skill-surface.py ; do not edit -->
# anycase command reference

Generated from `anycase commands --json` (version `1.3.2`). Every public command is listed.

## Global options

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Output formatted JSON for agent consumption |

## `anycase login`

Log in securely by reading Jacky Platform token from stdin

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--token-stdin` | flag | no |  | Read token from stdin without echoing |

## `anycase logout`

Log out and remove local stored credentials

No arguments.

## `anycase whoami`

Display current authentication status and masked token

No arguments.

## `anycase doctor`

Check the local install, sign-in and backend and print a health report (add --json for JSON); with --repair-install, instead make this package the only installed copy, printing a JSON install report (no sign-in needed)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--repair-install` | flag | no |  | Replace the installed copy under ~/.jackyzhang.app/plugins/anycase with the package this binary runs from, remove older copies, and print a JSON install report; keeps login, settings and cases |

## `anycase clb`

Calculate Canadian Language Benchmark (CLB) level from exam scores

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--test` | option | no |  | Test type (ielts, celpip, pte, tef, tcf) |
| `--listening` | option | no |  | Listening score |
| `--reading` | option | no |  | Reading score |
| `--writing` | option | no |  | Writing score |
| `--speaking` | option | no |  | Speaking score |
| `--skill` | option | no |  | Single skill name (listening, reading, writing, speaking) |
| `--score` | option | no |  | Single skill score |

## `anycase caselaw`

Search Canadian Federal Court precedent legal ratios and citations

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<QUERY>` | positional | yes |  | Search query or legal issue |
| `--court` | option | no | `fc`, `fca`, `irb`, `scc` | Court: fc, fca, irb, or scc |
| `--since` | option | no |  | Earliest judgment date (YYYY-MM-DD) |
| `--until` | option | no |  | Latest judgment date (YYYY-MM-DD) |
| `--mode` | option | no | `keyword`, `semantic`, `hybrid` | Search mode: keyword, semantic, or hybrid (both) |
| `--top` | option | no |  | Maximum results to retrieve (1-20) |
| `--questions` | option | no |  | Path to this-question questions JSON (host-written) |

## `anycase policy`

Search official IRCC Help Centre Q&As

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<QUERY>` | positional | yes |  | Search query or policy topic |
| `--lang` | option | no | `en`, `zh` | Language of the Help Centre answers to search |
| `--mode` | option | no | `keyword`, `semantic`, `hybrid` | Search mode: keyword, semantic, or hybrid (both) |
| `--top` | option | no |  | Maximum results to retrieve (1-20) |
| `--questions` | option | no |  | Path to this-question questions JSON (host-written) |

## `anycase manual`

Search IRCC Program Delivery Instructions and operational manuals

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<QUERY>` | positional | yes |  | Search query |
| `--policy-code` | option | no |  | Optional policy code to narrow the search |
| `--mode` | option | no | `keyword`, `semantic`, `hybrid` | Search mode: keyword, semantic, or hybrid (both) |
| `--top` | option | no |  | Maximum results to retrieve (1-20) |
| `--questions` | option | no |  | Path to this-question questions JSON (host-written) |

## `anycase pnp`

Search Provincial Nominee Program (PNP) policies, guides, and points grids

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<QUERY>` | positional | yes |  | Search query or provincial immigration topic |
| `--province` | option | no | `bc`, `on`, `ab`, `sk`, `mb`, `ns`, `nb`, `nl`, `pe`, `yt`, `nt` | Province code (e.g. bc, on, ab, sk, mb, ns, nb, nl, pe, yt, nt) |
| `--stream` | option | no |  | Optional stream name (e.g. tech, healthcare, international-graduate) |
| `--mode` | option | no | `keyword`, `semantic`, `hybrid` | Search mode: keyword, semantic, or hybrid (both) |
| `--top` | option | no |  | Maximum results to retrieve (1-20) |
| `--questions` | option | no |  | Path to this-question questions JSON (host-written) |

## `anycase coverage`

Show live corpus coverage (fetched now, never a bundled catalog)

No arguments.

## `anycase knowledge`

Search your private confirmed knowledge

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<QUERY>` | positional | yes |  | Search query |
| `--top` | option | no |  | Maximum results to retrieve (1-10) |
| `--questions` | option | no |  | Path to this-question questions JSON (host-written) |

## `anycase notes`

Search synthesized Canadian practitioner field notes and operational insights

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<QUERY>` | positional | yes |  | Search query or operational topic |
| `--top` | option | no |  | Maximum results to retrieve |
| `--questions` | option | no |  | Path to this-question questions JSON (host-written) |

## `anycase query`

Unified agent query router for AI tool calling

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--action` | option | yes |  | Action type: caselaw, policy, notes, knowledge, clb, manual, coverage, pnp |
| `--input` | option | no |  | Query string or JSON input for the action |
| `--top` | option | no |  | Top results count |
| `--lang` | option | no |  | Language code |
| `--policy-code` | option | no |  | Optional policy code (manual action) |
| `--province` | option | no |  | Optional province code (pnp action, e.g. bc, on) |
| `--stream` | option | no |  | Optional PNP stream name (pnp action) |
| `--mode` | option | no | `keyword`, `semantic`, `hybrid` | Search mode for caselaw, manual, and pnp: keyword, semantic, or hybrid (both) |

## `anycase commands`

Print the full public command surface of this version as JSON (every command with its summary and arguments); makes no network call and changes nothing

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Accepted for symmetry; the output is always JSON |

## `anycase feedback`

Submit feedback or issue report (Tell Jacky)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--type` | option | no |  | Feedback type (feature, bug, tip) |
| `--title` | option | yes |  | Title of feedback |
| `--description` | option | yes |  | Detailed description |
| `--user-confirmed` | flag | no |  | Mandatory confirmation from user before recording |

## `anycase check-eligibility`

Check applicant and job offer eligibility against program rulesets

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--profile` | option | yes |  | Path to client profile JSON (or '-' for stdin) |
| `--rules` | option | no |  | Path to ruleset YAML file (mutually exclusive with --program and --policy-revision) |
| `--program` | option | no |  | Program identifier (e.g. bcpnp_si_skilled_worker) |
| `--policy-revision` | option | no |  | Pin a VKS policy revision id (requires VKS authority mode: the production default, or ANYCASE_VKS_POLICY_URL) |
| `--format` | option | no |  | Output format: summary (human-readable) or json |

## `anycase programs list`

List all available Canadian immigration programs in catalog

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--jurisdiction` | option | no |  | Filter by jurisdiction (e.g. CA-BC, CA-NS, FED, or ALL) |
| `--category` | option | no |  | Filter by program category keyword |
| `--format` | option | no |  | Output format: summary (human-readable) or json |
| `--json` | flag | no |  | Output raw JSON |

## `anycase programs describe`

Show detailed metadata and requirements for a specific program

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<CATALOG_ID>` | positional | yes |  | Catalog ID of the program (e.g. nsnp_skilled_worker) |
| `--format` | option | no |  | Output format: summary (human-readable) or json |
| `--json` | flag | no |  | Output raw JSON |

## `anycase documents list`

List published official document-list capabilities

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Output raw JSON |

## `anycase documents resolve`

Resolve the exact active document-list pin for a pathway and stage; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--pathway-id` | option | yes |  | Pathway id from `documents list` |
| `--document-list-id` | option | yes |  | Stable document_list_id returned by `documents list` |
| `--stage` | option | no |  | Application stage (default eapr) |
| `--channel` | option | no |  | Channel (default `default`) |
| `--program-release-id` | option | no |  | Program Release ID for an exact historical pin |
| `--revision-id` | option | no |  | Exact document-list revision ID |
| `--payload-sha256` | option | no |  | Expected canonical payload SHA-256 |
| `--json` | flag | no |  | Output raw JSON |

## `anycase documents evaluate`

Resolve the active immutable list and locally evaluate its applicability against a fact profile; facts never leave the device

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--pathway-id` | option | yes |  | Pathway id from `documents list` |
| `--document-list-id` | option | yes |  | Stable document_list_id returned by `documents list` |
| `--stage` | option | no |  | Application stage (default eapr) |
| `--channel` | option | no |  | Channel (default `default`) |
| `--program-release-id` | option | no |  | Program Release ID for an exact historical pin |
| `--revision-id` | option | no |  | Exact document-list revision ID |
| `--payload-sha256` | option | no |  | Expected canonical payload SHA-256 |
| `--local-payload` | option | no |  | Evaluate an unpublished local canonical payload without network access; never treated as a published Program Release |
| `--facts` | option | yes |  | Local JSON fact profile; it is not sent to ImmiCore |
| `--as-of` | option | yes |  | Explicit evaluation date in YYYY-MM-DD format |
| `--jurisdiction` | option | no |  | Optional jurisdiction selector |
| `--locale` | option | no |  | Optional locale selector |
| `--json` | flag | no |  | Output the shared evaluator's structured JSON report |

## `anycase score policies`

List the published scoring grids (policy ids) with their revisions; needs sign-in

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--changed-since` | option | no |  | Only grids changed since this revision or ISO date substring |

## `anycase score inputs`

Show the input contract (fields to collect) for one scoring grid

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<POLICY_ID>` | positional | yes |  | Scoring grid id from `score policies` |

## `anycase score evaluate`

Evaluate one scoring grid on this device from an inputs JSON file; prints the score breakdown, or the missing inputs with a resume token

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--policy` | option | yes |  | Scoring grid id from `score policies` |
| `--inputs` | option | yes |  | Inputs JSON file shaped by `score inputs` |
| `--as-of` | option | no |  | Evaluation date YYYY-MM-DD; selects the grid revision in force that day |
| `--resume` | option | no |  | Resume token printed by an earlier evaluation that reported missing inputs |
| `--revision` | option | no |  | Exact immutable scoring revision; requires its payload hash |
| `--payload-sha256` | option | no |  | Expected canonical scoring payload SHA-256 |

## `anycase score rank`

Evaluate every full-coverage points grid from one inputs file and rank the results

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--inputs` | option | yes |  | Inputs JSON file |
| `--as-of` | option | no |  | Evaluation date YYYY-MM-DD |

## `anycase score whatif`

Re-evaluate one grid after changing some input fields, to show how the score moves

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--policy` | option | yes |  | Scoring grid id from `score policies` |
| `--inputs` | option | yes |  | Inputs JSON file |
| `--changes` | option | yes |  | JSON file of [{field, value}, ...] |
| `--as-of` | option | no |  | Evaluation date YYYY-MM-DD |

## `anycase score diff`

Compare two result files produced by `score evaluate`; fully offline

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `<OLD_RESULT>` | positional | yes |  | Earlier result JSON file |
| `<NEW_RESULT>` | positional | yes |  | Later result JSON file |

## `anycase case init`

Open a local case: pin the program release and stamp the template

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--program` | option | yes |  | Program code to pin: a catalog id, or the template's own program (e.g. canada.visitor.extension) |
| `--template` | option | yes |  | Built-in case template (e.g. visitor-extension) |
| `--case-id` | option | yes |  | Case id: lowercase letters, digits, dashes (e.g. case-2026-vr-001) |

## `anycase case show`

Pretty-print the case.v2.json document

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--case-dir` | option | yes |  | Case folder (or the case.v2.json path; a legacy case.v1.json is upgraded on load) |

## `anycase case update`

Typed intent update: value, "unknown", "none", or leave unset

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--case-dir` | option | yes |  | Case folder (or the case.v2.json path) |
| `--intent` | option | no |  | JSON file of intent keys merging into intent (typed strings or "unknown"/"none") |
| `--set` | option | no |  | Set one intent key: --set <key> <value>; use "unknown" for unknown, "none" where the form allows a blank. Keys: the IMM 5708 task inputs (e.g. visit_purpose, visit_to_date, bg_criminal; see 'case plan') plus representative |

## `anycase case derive`

Expand the template task pack and list unresolved intent items

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--case-dir` | option | yes |  | Case folder (or the case.v2.json path) |
| `--json` | flag | no |  | Output raw JSON |

## `anycase case plan`

Emit the declarative task package JSON for Bridge / AnyPDF / AnyWeb

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--case-dir` | option | yes |  | Case folder (or the case.v2.json path) |

## `anycase case db migrate`

Apply the checked-in schema to the PostgreSQL case store (idempotent)

No arguments.

## `anycase case db seed`

Mirror the built-in templates and their AnyPDF/AnyWeb/Bridge config into the database

No arguments.

## `anycase case db list`

List the cases stored in the database; read-only

No arguments.

## `anycase case db targets`

Show one template's engine targets and pin config; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--template` | option | yes |  | Template id (e.g. visitor-extension) |

## `anycase case db show`

Pretty-print one stored case; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--case-id` | option | yes |  | Stored case id (e.g. case-2026-vr-001) |

## `anycase case db init`

Open a case in the database, pinning the program release and stamping the template

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--program` | option | yes |  | Program code to pin: a catalog id, or the template's own program |
| `--template` | option | yes |  | Built-in case template (e.g. visitor-extension) |
| `--case-id` | option | yes |  | New case id: lowercase letters, digits, dashes |

## `anycase case db derive`

Expand the template task pack for one stored case and list unresolved items; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--case-id` | option | yes |  | Stored case id (e.g. case-2026-vr-001) |
| `--json` | flag | no |  | Output raw JSON |

## `anycase case db plan`

Emit the declarative task package JSON for one stored case; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--case-id` | option | yes |  | Stored case id (e.g. case-2026-vr-001) |

## `anycase case db update`

Apply a typed intent update to one stored case (CAS via --expect-revision)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--case-id` | option | yes |  | Stored case id (e.g. case-2026-vr-001) |
| `--intent` | option | no |  | JSON file of intent keys merging into intent (typed strings or "unknown"/"none") |
| `--set` | option | no |  | Set one intent key: --set <key> <value>; use "unknown" for unknown, "none" where the form allows a blank. Keys: the IMM 5708 task inputs (e.g. visit_purpose, visit_to_date, bg_criminal; see 'case plan') plus representative |
| `--expect-revision` | option | no |  | Expected current revision; a mismatch refuses the write (defaults to the stored revision) |

## `anycase case db delete`

Delete one stored case (children cascade; a missing case is an error)

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--case-id` | option | yes |  | Stored case id (e.g. case-2026-vr-001) |

## `anycase pair join`

Join a live diagnosis session with the code Jacky's assistant gave the user, once the user has agreed; opens a connection and returns its status

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--code` | option | yes |  | Join code from Jacky's assistant |
| `--user-confirmed` | flag | no |  | Required: the user has agreed to let Jacky's assistant see this machine's AnyCase status |
| `--json` | flag | no |  | Output raw JSON |

## `anycase pair status`

Show whether a pair connection is open; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Output raw JSON |

## `anycase pair snapshot`

Send the current product envelope (status snapshot, never case or retrieval text) to the connected assistant

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--envelope-json` | option | yes |  | Envelope JSON file, or '-' for stdin |
| `--json` | flag | no |  | Output raw JSON |

## `anycase pair inbox`

Show unread instructions from Jacky's assistant; read-only

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--json` | flag | no |  | Output raw JSON |

## `anycase pair read`

Mark one instruction as read after it has been shown to the user

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--message-id` | option | yes |  | Message id taken from `pair inbox` |
| `--json` | flag | no |  | Output raw JSON |

## `anycase pair result`

Send the outcome of an action the assistant asked for

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--ok` | flag | no |  | Whether the requested action succeeded |
| `--error` | option | no |  | Short error text when the action failed |
| `--envelope-json` | option | no |  | Optional envelope JSON file, or '-' for stdin |
| `--json` | flag | no |  | Output raw JSON |

## `anycase pair close`

End the pair connection

| Argument | Kind | Required | Values | Meaning |
|---|---|---|---|---|
| `--reason` | option | no |  | Short reason recorded with the close |
| `--json` | flag | no |  | Output raw JSON |
