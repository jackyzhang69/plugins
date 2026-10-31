# AnyDoc saved packing rules — host agent contract

The human changes saved packing rules in this chat. They state the rule in
everyday words and confirm a plain-language readback. They never open, edit, or
paste JSON, and they never run a command.

This playbook is the only path that may create, replace, or forget a private
assembly model. Customer names, file paths, case facts, document maps, and
execution plans never go to accountd.

## Talk to the human

1. **Plain language.** Say the rule you will save — not flags, JSON, or hashes.
2. **What / next.** “I will add: if the applicant is under 18, skip the police
   certificate. Confirm?” Then wait.
3. **JSON is for tools.** Never paste a model file into chat. Never ask them to
   open an export.
4. **Never tell the human to run a command.** Never send them to a terminal.

When stdout is `jz.plugin.envelope.v1`, follow its exact `status` and
`continue_args` until the product reports `ready`, then resume this request.
`needs_agent` is silent by default: satisfy the declared need (usually
[connect](connect.md)), then re-invoke the continuation. Do not assign doctor
homework.

## Never hand JSON to the human

Never ask the human to open, edit, or paste JSON. Never tell the human to run a
command. Drafts live only in host scratch (an OS temporary directory the human
does not see). Never write them to the Desktop, Downloads, or the client
folder. After save, replace, forget, a refused run, or a stop, delete every
scratch draft and export immediately.

`models export` writes a full saved record. `models validate` / `models save` /
`models replace` accept only a typed draft (`contract`, `case_type`, `title`,
`roles`, `conditions`, `deliverables`). Do not pass an export file to those
commands. The host strips server fields in scratch.

The standalone model draft API always requires `contract`. Plan v4 embeds a
draft under `authority.list` or a saved model under `authority.model`; its
local-only omitted-contract rule, exact digest, and full plan shapes are in the
[Plan v4 contract](plan-v4.md#plan-v4-contract).

## Draft JSON (the only accepted shape)

A draft has exactly these six top-level keys and no others. `model_id`,
`revision`, and `model_hash` are accountd-owned: they appear only in
`models export` output, and including them in a draft is rejected.

| Key | Rule |
|---|---|
| `contract` | Required. Exactly the literal string `anydoc-assembly-model-v2`. Missing or different → `model_invalid`. |
| `case_type` | Matches `^[a-z0-9][a-z0-9._-]{0,127}$` |
| `title` | 1–120 characters |
| `roles` | 1–32 entries: `{role_id, label, cardinality}`. `role_id` matches `^[a-z][a-z0-9_]{0,63}$` and is unique; `label` is 1–100 characters; `cardinality` is `"one"` or `"many"`. |
| `conditions` | Any number of entries: `{condition_id, label, scope}`. `condition_id` uses the `role_id` format and is unique; `label` is 1–200 characters; `scope` is `"case"` or `"subject"`. |
| `deliverables` | 1–200 entries, one per final output. |

A deliverable is `{deliverable_id, sequence, filename_template, output_mode,
serves_roles, when, contents}`:

- `deliverable_id` uses the `role_id` format and is unique.
- `sequence` matches `^[0-9]{1,3}[A-Z]?$` and is unique; it orders the outputs.
- `filename_template` renders a complete base filename with an extension
  (`<stem>.<ext>`, both sides non-empty), at most 180 characters and 240 bytes,
  using portable filename characters only. `per_person` needs `{person_name}`
  exactly once, `single` needs no `{person_name}`, and no other placeholder is
  allowed.
- `output_mode` is `"single"` or `"per_person"`.
- `serves_roles` is a non-empty list of declared `role_id`s without repeats.
- `contents` is non-empty.

A content is `{content_id, label, subject_mode, subject_roles, when}`.
`content_id` uses the `role_id` format and is unique across the whole model;
`label` is 1–160 characters; `subject_mode` is `"shared"` or `"per_person"`;
`subject_roles` is a non-empty list of declared `role_id`s without repeats.

`when` is `{"kind": "always"}` or
`{"kind": "condition", "condition_id": "<declared condition_id>"}`. `always`
carries no `condition_id`; `condition` needs a declared one. A `subject`-scoped
condition needs person context: for a deliverable that means
`output_mode = "per_person"`; for a content it means its own
`subject_mode = "per_person"` or a per-person parent deliverable. A
`case`-scoped condition is valid anywhere.

The whole draft must serialize to at most 256 KiB.

This minimal draft is valid as written:

```json
{
  "contract": "anydoc-assembly-model-v2",
  "case_type": "ca.visitor_visa.outside_canada",
  "title": "Visitor visa — outside Canada",
  "roles": [{ "role_id": "applicant", "label": "Applicant", "cardinality": "many" }],
  "conditions": [
    {
      "condition_id": "minor_child",
      "label": "Applies when the applicant is a minor child",
      "scope": "subject"
    }
  ],
  "deliverables": [
    {
      "deliverable_id": "passport",
      "sequence": "01",
      "filename_template": "Passport - {person_name}.pdf",
      "output_mode": "per_person",
      "serves_roles": ["applicant"],
      "when": { "kind": "always" },
      "contents": [
        {
          "content_id": "passport_bio_page",
          "label": "Passport bio page",
          "subject_mode": "per_person",
          "subject_roles": ["applicant"],
          "when": { "kind": "always" }
        },
        {
          "content_id": "parental_consent",
          "label": "Parental consent",
          "subject_mode": "per_person",
          "subject_roles": ["applicant"],
          "when": { "kind": "condition", "condition_id": "minor_child" }
        }
      ]
    }
  ]
}
```

Adapt the shape to the user's confirmed rule; never paste this JSON into chat.
`models validate` prints `valid: true` plus `draft_sha256`; the digest stays in
the host's confirmation receipt. The human confirms the plain-language readback
only.

## Change a saved model

A model that already exists changes only through this replace path. Do not
Teach Me because the current folder looks different.

1. Identify the exact `case_type`. Run
   `"$ANYDOC_BIN" models resolve --case-type <exact-case-type> --json`.
2. On `model_found`, keep `model_id`, `revision`, and `model_hash`. On
   `model_absent`, use Teach Me in [assemble](assemble.md) — still never hand
   JSON to the human. On `model_unavailable` / `model_invalid` / ambiguous
   case type, stop the model flow as assemble.md requires.
3. Read the current model with
   `"$ANYDOC_BIN" models show --id <model-id> --json`.
4. In scratch, write a draft that matches the user's request. Keep only
   reusable structure: ordered deliverables, contents, roles, and named
   conditions. No customer names, paths, or this-case facts.
5. Run `"$ANYDOC_BIN" models validate --model <scratch-draft.json> --json`.
   Fix the draft yourself until it is valid.
6. Show the **entire** model in plain language: every ordered output filename
   pattern, included content, role, and named condition. Partial summaries are
   not confirmation.
7. After an explicit yes, run
   `"$ANYDOC_BIN" models replace --id <model-id> --expected-revision <revision> --model <scratch-draft.json> --user-confirmed --json`.
8. Report the new revision in one sentence. Delete scratch files.

If they refuse, delete scratch and leave the saved model unchanged.

## New model (only after `model_absent`)

Follow Teach Me in [assemble](assemble.md). The same JSON and command bans
apply. After they confirm the entire model, run
`"$ANYDOC_BIN" models save --model <scratch-draft.json> --user-confirmed --json`,
then delete scratch.

## Forget

Only after a separate explicit confirmation, run
`"$ANYDOC_BIN" models forget --id <model-id> --expected-revision <revision> --user-confirmed --json`.
Do not treat a packing-rule change as permission to forget.
