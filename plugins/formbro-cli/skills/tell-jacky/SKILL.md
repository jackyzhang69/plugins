---
name: tell-jacky
description: Submit a feature request, bug report, or knowledge tip to FormBro on the user's behalf ("Tell Jacky"). Collects title/description/context/URL/images from the conversation, always shows a draft for user confirmation before sending, and follows PII-safe defaults since FormBro handles immigration case data. Requires connect-formbro to have been run first, and a write-scoped token.
when_to_use: |-
  Trigger phrases:
    - "tell Jacky about this"
    - "report this bug / file a bug report"
    - "this would be a good FormBro feature request"
    - "note this as a tip for FormBro" / "this is a useful tip, save it"
    - the agent itself hits a FormBro CLI error worth reporting and the user agrees to send it
---

# Tell Jacky

Submits feedback to the same backend `Feedback` system FormBro's desktop/web users use, via the
bundled `formbro` CLI. Resolve the binary once via `formbro-capabilities/SKILL.md` §B before
using this skill.

**Cardinal rule:** every submission goes through `<formbro> feedback create`. Never call the
FormBro backend directly. Never send anything to `feedback create` without showing the user the
exact title/description/context/images first and getting explicit confirmation — this is not a
"proceed automatically" write like a routine patch; see the confirmation section below.

## Data structure

| Field | Required | Maps to |
|---|---|---|
| `type` | yes | one of `feature-request`, `bug-report`, `knowledge-tip` (CLI flag values; backend stores `feature_request`/`bug_report`/`knowledge_tip`) |
| `title` | yes | short summary, ≤200 chars |
| `description` | yes | the full text — the more concrete the better, especially for bug reports |
| `url` | no | a relevant page/portal URL; merged into `context.page_url` |
| `context-json` | no | escape hatch for extra structured fields (see below); merged under `context` |
| `image` | no | local screenshot path(s), repeatable; uploaded after create |

Useful `--context-json` fields (all optional): `program_key`, `application_id`, `entity_type`,
`action_type`, `error_message`. `context.source` is always stamped `"formbro-cli"` by the CLI
itself — you cannot and should not try to set it.

## Router (user intent → exact command)

| If the user says… | Draft, confirm, then run |
|---|---|
| "tell Jacky about this bug" / agent hit a CLI error worth reporting | `<formbro> feedback create --type bug-report --title "<short>" --description "<verbatim error + what you were doing>" [--url <portal url>] [--context-json '{"error_message":"<verbatim>","application_id":"<id>"}']` |
| "file a feature request" | `<formbro> feedback create --type feature-request --title "<short>" --description "<what and why>"` |
| "this is a good tip, save it" | `<formbro> feedback create --type knowledge-tip --title "<short>" --description "<the tip>"` |
| any of the above + user shared a screenshot | add one `--image <path>` per file, confirmed individually (see PII section) |

## Proactively gathering the data

- If the user didn't give a title, propose a one-line title yourself from context and show it in
  the confirmation step — don't silently invent one and submit without showing it.
- For bug reports triggered by a failed FormBro CLI command: capture the **verbatim** JSON error
  output into `description` and/or `context.error_message` — do not paraphrase or summarize it
  away. Verbatim text is what the `bug_report` auto-triage (system_errors correlation) actually
  keys off of.
- Don't chase completeness for its own sake. A title + description is a valid, submittable
  feedback entry — `url`, `context-json`, and `image` are enrichments, not requirements.

## Mandatory draft confirmation — every submission, no exceptions

Before running `feedback create`, always show the user:
1. The exact `type`, `title`, `description`.
2. The exact `context` fields you're about to send (including `url` if set).
3. The list of image paths you're about to attach, if any.

Then wait for explicit go-ahead. This applies to every submission — feature requests and
knowledge tips too, not just bug reports flagged as "sensitive." FormBro handles immigration case
data; nothing leaves the user's machine without them seeing it first.

## PII guidance

- Do **not** auto-populate `context` with case identifiers (`applicant_id`, `employer_id`,
  `application_id`) or verbatim applicant field values (names, dates of birth, case numbers)
  unless the user explicitly asks you to include them.
- When capturing verbatim error text for a bug report, scan it for anything that looks like
  personal data (names, SIN-like numbers, emails, case document contents) and flag it to the user
  as part of the confirmation step rather than silently including or silently stripping it — let
  the user decide.
- When a screenshot is offered, ask what's in it before attaching if it's not obviously just a UI
  error state (e.g. a full case form is a bigger disclosure than a single error toast). Attach
  only after the user confirms that specific image.

## After submitting

- `bug_report` (submitted with `context`) auto-correlates to a `system_errors` row and shows up in
  the admin **Problems/Incidents** page.
- `feature_request` and `knowledge_tip` show up only in **Feedback Management** — they never
  appear in Problems. Don't read "not in Problems" as a failure; that's expected for these two
  types.
- On success, the CLI prints the created feedback (with an `id`) and, if images were attached, a
  per-image `status: "ok"` or `status: "error"` result — report any per-image failures to the
  user rather than only reporting overall success.

## Failure handling

- **403 with a scope message** (`"Token scope [...] insufficient; need 'write' ..."`): the
  connected token only has `read` scope. Tell the user: enable **write** scope on your existing
  FormBro API token at Settings → API Tokens (edit in place — no need to regenerate), then retry.
- Any other error: surface the CLI's structured error verbatim (see `output::print_error`
  conventions used across every FormBro command) rather than guessing at a fix.
