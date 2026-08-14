---
name: tell-jacky
description: Submit a feature request, bug report, or knowledge tip to FormBro on the user's behalf ("Tell Jacky"). Collects title/description/context/images from the conversation, always shows a draft for user confirmation before sending, and follows PII-safe defaults since FormBro handles immigration case data. Requires connect-formbro to have been run first, and a write-scoped token.
when_to_use: |-
  Trigger phrases:
    - "tell Jacky about this"
    - "report this bug / file a bug report"
    - "this would be a good FormBro feature request"
    - "note this as a tip for FormBro" / "this is a useful tip, save it"
    - the agent itself hits a FormBro CLI error worth reporting and the user agrees to send it
---

## Shared platform token (host agent — mandatory)

- Canonical durable user credential: `~/.jackyzhang.app/token/user.json` (`jz_` only).
- **One connect for the whole platform.** If this file already exists from any other official plugin, do **not** ask the human to log in again.
- On load, if `user.json` is missing but a legacy on-disk `jz_` exists (`token/user.json`, product-local config token fields), **migrate immediately** by running this product's CLI `whoami`/`doctor`/any authenticated command (load-time migrate is built in). User must feel no re-paste.
- Only when no migratable `jz_` exists: complete connect once using the token-delivery rules below. After that, every registered plugin reuses the same slot.
- Never print or log the raw token. Prefer masked doctor/whoami output.
- Do not create product-local durable token files.

## Token delivery to the host agent (connect) — LOCKED 2026-08-14

The host agent performs connect **for** the human. **Never** tell the human to open a terminal and run login commands themselves.

Accept input in this order:

1. **File containing the token (preferred).** If the human provides a filesystem path (e.g. `~/Desktop/jacky-token.txt`) or an attached/readable file whose contents are a single `jz_…` value (optional surrounding whitespace/newline only):
   - Read the file in the agent tool channel.
   - Pipe the token to the product CLI via stdin only: `login --token-stdin` (or the product's equivalent).
   - Do **not** put the token on argv, in chat echo, in logs, or in screenshots.
   - Confirm success with masked doctor/whoami only.

2. **Plaintext token in chat (allowed, discouraged).** If the human pastes a raw `jz_…` into the conversation:
   - **Warn once** in plain language: prefer a local file path next time so the secret is not retained in chat history; do **not** refuse.
   - Proceed immediately: feed that value to `login --token-stdin` via a non-echoing agent-side pipe (temp file mode 0600 deleted after use is OK; never `echo TOKEN |` in a way that lands in shell history if avoidable).
   - Do not repeat the raw token back in the reply.

3. **No token yet.** Ask the human to send either a **file path** (best) or paste the token. Still do not ask them to run terminal commands.

Hard rules:

- `--token <value>` / argv secrets remain **forbidden** for the CLI.
- Agent may read a user-supplied path and stdin-feed the CLI; that is the supported file path.
- After any successful connect, other plugins must not re-prompt when `user.json` is present.


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
| `application-id` | required for `bug-report` | exact FormBro application ID; backend verifies it belongs to the submitting user |
| `context-json` | no | escape hatch for extra structured fields (see below); merged under `context` |
| `image` | no | local screenshot path(s), repeatable; uploaded after create |

Useful `--context-json` fields (all optional): `program_key`, `entity_type`,
`action_type`, `error_message`. `context.source` is always stamped `"formbro-cli"` by the CLI
itself — you cannot and should not try to set it.

## Router (user intent → exact command)

| If the user says… | Draft, confirm, then run |
|---|---|
| "tell Jacky about this bug" / agent hit a CLI error worth reporting | `<formbro> feedback create --type bug-report --title "<short>" --description "<verbatim error + what you were doing>" --application-id "<exact application id>" [--context-json '{"error_message":"<verbatim>"}']` |
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
- Every bug report must be bound to the exact application involved. If the user supplied an
  applicant/entity ID instead, stop and ask for the application ID. Feature requests and
  knowledge tips still require only title + description.

## Mandatory draft confirmation — every submission, no exceptions

Before running `feedback create`, always show the user:
1. The exact `type`, `title`, `description`.
2. The exact `context` fields you're about to send (including `url` if set).
3. The list of image paths you're about to attach, if any.

Then wait for explicit go-ahead. This applies to every submission — feature requests and
knowledge tips too, not just bug reports flagged as "sensitive." FormBro handles immigration case
data; nothing leaves the user's machine without them seeing it first.

## PII guidance

- For a bug report, include only the required exact `application_id`; do not add applicant IDs,
  employer IDs, or verbatim applicant field values unless the user explicitly asks.
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
  FormBro API token at the account Portal at https://jackyzhang.app/account/tokens (edit in place — no need to regenerate), then retry.
- Any other error: surface the CLI's structured error verbatim (see `output::print_error`
  conventions used across every FormBro command) rather than guessing at a fix.
