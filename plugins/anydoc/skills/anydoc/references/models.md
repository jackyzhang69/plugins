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
