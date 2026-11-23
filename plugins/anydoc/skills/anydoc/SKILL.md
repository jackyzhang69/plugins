---
name: anydoc
description: >-
  READ THIS FIRST for AnyDoc. Pack this folder, assemble the upload package,
  inspect this client directory, rename and merge these PDFs, build the IRCC
  document folder. Change the saved packing rules / update the assembly model.
  Connect / log in / save my portal token for saved models and tell Jacky.
  connect with Jacky / pair session / join code from Jacky. Offline inspect
  and manual plans. One discovery file; playbooks in references/. Ask the
  live CLI (`anydoc commands --json`), never from memory of an older release.
when_to_use: |-
  Load on plugin start. Trigger phrases: "pack this folder",
  "assemble the upload package", "inspect this client directory",
  "rename and merge these PDFs", "build the IRCC document folder",
  "change the saved packing rules", "update the assembly model",
  "connect to anydoc / log in to anydoc / save my portal token",
  "tell Jacky about this", "report this AnyDoc bug / file a bug report",
  "feature request for AnyDoc", "note this as a tip",
  "connect with Jacky / pair session / join code from Jacky".
---

# AnyDoc — host agent contract

Load this on plugin start and whenever the user asks to inspect, plan, assemble,
or verify a client document folder, change the saved packing rules, connect, or
Tell Jacky.

AnyDoc organizes the final forms and supporting documents that the user intends
to submit to IRCC or another receiving institution. Its native reader inspects
every supported source first, extracts available page text, and records previews
and protection facts before the host makes semantic decisions. It does not
classify documents, does not run OCR, and never claims a package is ready to
submit.

## Talk to the human

On the first session after install, read [get-started](references/get-started.md)
before the first user-visible reply. Brief the human in plain language — what
AnyDoc does and what to try first — not a command list. After a version bump,
read [whats-new](references/whats-new.md), tell the human what changed in one
breath, then resume their original intent without asking them to repeat the
request. Never ask the human to run doctor as homework. Run the intended ordinary
command first; when stdout is `jz.plugin.envelope.v1`, follow its exact `status`
and `continue_args` until the product reports `ready`, then resume the sealed
request.

Use everyday words. Say what you found and what they must confirm — not flags,
JSON, or file hashes. They decide names, page order, and what to keep or drop.
Never tell the user a package can be filed or that an application is complete.

## When the user asks "what can you do?"

Do not answer from a frozen command list. Run the live client and translate
`commands --json` into product language:

```bash
"$ANYDOC_BIN" commands --json
```

## Agent router — intents

| User intent | Host does | Human may be asked |
|---|---|---|
| "what can AnyDoc do" | Live `"$ANYDOC_BIN" commands --json`; translate inspect/plan/assemble capabilities | Connect when resolving or saving a private model ([connect](references/connect.md)) |
| pack this folder / assemble the upload package / inspect this client directory / rename and merge these PDFs / build the IRCC document folder | [assemble](references/assemble.md): inspect → plan → approve → assemble → verify | Document list or confirmed private model; explicit yes on the packing list; facts for ambiguous subjects |
| change the saved packing rules / update the assembly model | [models](references/models.md): host edits scratch, validates, replaces after a yes | Confirm the new rules in plain language |
| connect to anydoc / log in / save my portal token | [connect](references/connect.md): pipe Portal token via stdin | Token file path or one-time paste (never argv) |
| tell Jacky / report bug / feature / tip | [tell-jacky](references/tell-jacky.md) | Confirm the exact draft before send |
| connect with Jacky / pair session / join code from Jacky | [pair-session](references/pair-session.md) | Confirm once that Jacky's assistant may look at this machine's AnyDoc inspect and assemble status |

Playbooks: [connect](references/connect.md), [assemble](references/assemble.md),
[models](references/models.md), [tell-jacky](references/tell-jacky.md),
[pair-session](references/pair-session.md).

Plan v4 field contract and complete local examples:
[references/plan-v4.md](references/plan-v4.md#plan-v4-contract).

## Live CLI discovery (fail-closed)

Ask the live CLI, never from memory of an older release:

```bash
"$ANYDOC_BIN" commands --json
```

Every public command, with its key flags. All of them accept `--json`; stdout is
always JSON. For every argument of every command, ask the live CLI with
`commands --json`.

- `doctor` — local health report, no login. `doctor --repair-install` installs this package as the one current copy (see Updates).
- `inspect --input <folder>` — mechanical facts for every file under a client folder; `--detail-level summary` is the only level this version supports.
- `render-page --input <pdf> --page <n> --output <new.png>` — higher-resolution PNG preview of one PDF page; `--max-dimension` sets the longest edge.
- `extract-existing-text --input <pdf>` — the text layer a PDF already has, never OCR; `--page` picks one page, `--max-chars` caps the output.
- `plan validate --plan <file> --input <folder>` — check a Plan v4 and name any bad field path.
- `plan show --plan <file> --input <folder>` — reviewable preview of the plan and its approval hash.
- `plan approve --plan <file> --receipt <new file> --input <folder>` — record the user's yes as a receipt bound to that exact plan.
- `assemble --input <folder> --plan <file> --receipt <file>` — write the approved outputs as a new `anydoc-output/vNNN` snapshot.
- `verify --input <folder> --plan <file> --receipt <file>` — mechanically check the written outputs against the plan.
- `resume --input <folder>` — finish an interrupted assemble from its journal.
- `clean --input <folder>` — delete scratch, staging and journal files only.
- `login --token-stdin` — save the Portal token read from stdin; `--force` replaces an existing valid one.
- `logout` — clear the local login. `whoami` — check the login and show a masked identity.
- `guides list [--pathway-id <id> --stage <stage> --channel <channel>]` — bundled conventions, or scoped public guide revisions.
- `guides show [--id <id>] [--pathway-id <id> --stage <stage>]` — one bundled convention or the current public revision.
- `guides revision --pathway-id <id> --stage <stage> --document-list-id <id> --program-release-id <id> --document-list-revision-id <id> --payload-sha256 <hash>` — one exactly pinned public revision.
- `guides evaluate --facts <file> --as-of <date>` with the pathway, stage and pin flags — compare a pinned guide with local facts; choose `--case-type` with `--bindings`, `--private-list-model` with `--bindings`, or `--public-only-confirmed`; `--decisions` supplies dispositions.
- `models list` — saved private models. `models show --id <id>` — one model. `models resolve --case-type <type>` — find the model for a case type (`model_absent` when none).
- `models validate --model <draft>` — check a draft locally. `models save --model <draft> --user-confirmed [--idempotency-key <key>]` — create a model. `models replace --id <id> --expected-revision <n> --model <draft> --user-confirmed` — new revision. `models forget --id <id> --expected-revision <n> --user-confirmed` — delete. `models export --id <id> --output <new file>` — write the full record locally.
- `feedback create --type <kind> --title <t> --description <d> --user-confirmed` — Tell Jacky; `feedback list` and `feedback status --id <id>` track items; `feedback inbox` and `feedback read --update-id <id>` handle replies.
- `pair join --code <code> --user-confirmed` — join Jacky's assistant; `pair status`, `pair snapshot --envelope-json <file>`, `pair inbox`, `pair read --message-id <id>`, `pair result --ok [--error <text>]` and `pair close [--reason <text>]` run the session.
- `commands --json` — the live command surface.
- `photos` — a real command that exists in this CLI but is reserved: calling it returns `not_implemented` with exit code 3. Photo sheets are made by the `render_photo_document` plan action instead.
- `office-preview` — a real command that exists in this CLI but is reserved: calling it returns `not_implemented` with exit code 3.
- `guide-feedback` — a real command that exists in this CLI but is reserved: calling it returns `not_implemented` with exit code 3.

Resolve the binary once per session, then export `ANYDOC_BIN`:

1. `$ANYDOC_BIN` if already set.
2. Canonical install under `$JACKYZHANG_APP_HOME/plugins/anydoc/current/bin/...`.
3. If missing, run `"$PACKAGE_BIN" doctor --repair-install --json`, then use the canonical path.
4. Do not prefer a random `anydoc` on PATH over the canonical tree.

Runtime data is `~/.jackyzhang.app/anydoc/`. Credentials are
`~/.jackyzhang.app/token/user.json`. Never a second product token file.

## Non-negotiable rules

1. **All packing goes through the AnyDoc CLI.** Do not copy files yourself to “help.”
2. **Approve before assemble.** Unapproved plans are refused.
3. **No packing list without a named authority. No plan without a named authority.** Plan v4 accepts exactly four `authority.kind` values: `private_model`, `manual_private_list`, `public_guide`, and `manual_plan`. A saved `private_model` remains preferred when available. A user-provided list can stay local as `manual_private_list`; AnyDoc binds its typed definition by digest and may suggest saving it separately, but never saves it automatically. Saving requires a separate explicit model-save confirmation. `public_guide` is available only after the human explicitly chooses public-only with `--public-only-confirmed`; `pilot_partial` or incomplete/unknown evaluated scope also requires `--partial-scope-acknowledged`. A guide used for comparison does not replace the selected private authority. A model error never changes authority automatically: stop and report it. `manual_plan` is for outputs the user directly specifies. Filenames and folders are not authority. A folder listing is not a document list.
4. **Encrypted, form, or signed PDFs:** copy or rename only — no split/merge/rotate/compress.
5. **Do not ask the AnyDoc binary to OCR.** Use native text first; read unresolved scans in this primary session only.
6. **Teach Me (only after `model_absent`).** You actually ran `models resolve` in this session and it returned the literal state `model_absent`. Never open Teach Me without that call. A non-zero exit, a timeout, a network or auth error, unparsable output, or any server error is **not** `model_absent`. No other state starts Teach Me. A finished package, snapshot, folder listing, or Public Guide is not a teachable source.
7. **Client intake questionnaires never reach an assembly output, in any
  format.** Form fields, a signature, an official-looking layout, or an official-looking filename never promote intake
  material into a deliverable. Excluded means excluded from every output: never a page inside a merged or combined PDF. Honesty table: Client intake questionnaire (any format).
8. **Saved packing rules change in this chat.** The human states the rule and confirms the readback. Never ask them to open, edit, or paste JSON, and never tell them to run a command. Follow [models](references/models.md).

Full assemble workflow, Teach Me, plan actions, and honesty table:
[assemble](references/assemble.md).

### Public Guide comparison

- `guides list --pathway-id ... --stage ... [--channel default]` lists scoped public revisions; unscoped `guides list` still shows bundled assembly conventions.
- `guides show --id ... --pathway-id ... --stage ... [--channel default]` resolves the current exact public revision. `guides revision` requires the complete release/revision/hash pin and its owner scope.
- `guides evaluate` requires local facts and an explicit `--as-of` date. Use `--jurisdiction` or `--locale` when the pinned scope requires them; an omitted restricted dimension stays `context_incomplete`. Select exactly one path: `--case-type` plus local `--bindings` for an available saved private model; `--private-list-model` plus local bindings for an unsaved private list; or `--public-only-confirmed` for public-only use. `--decisions` supplies local dispositions for listed differences. These local files never go to the service.
- For `pilot_partial` or incomplete/unknown evaluated scope, public-only planning also requires `--partial-scope-acknowledged`. Comparison keeps the chosen private authority; public-only use is a separate opt-in. The result keeps the exact pin, facts digest, evaluation, source pins, and comparison decisions for a Plan v4. A separate `plan approve` is still required before assembly; source-pending and official-pending remain distinct.
- A Plan v4 may carry a local `official_case_specific` checklist overlay. Treat its typed slots and additional requests as local unresolved prompts (`case_specific_pending`), separate from official guide facts and status. Unknown never means `not_applicable`; the overlay does not prove AnyDoc accessed an account and is not uploaded. Its source hash is approval-bound, so a changed file requires a newly reviewed plan.

## Updates

`anydoc` may print one stderr line with schema `jz.plugin.update_notice.v1`. It never blocks a command. Finish what the user asked first, then tell them once, in plain words, that a newer version is available and ask whether to update now. On yes: refresh this plugin through the host (Claude Code: `claude plugin marketplace update jacky-plugins`, then `claude plugin update anydoc@jacky-plugins`; Codex: `codex plugin marketplace upgrade jacky-plugins`, then `codex plugin add anydoc@jacky-plugins`; other hosts: attach the new `plugins/anydoc/` tree). Then run `doctor --repair-install` with the binary from the **new** package, not the folder this session started from (Claude Code: `~/.claude/plugins/cache/jacky-plugins/anydoc/<latest>/bin/<platform>/anydoc`; Codex: `~/.codex/plugins/cache/jacky-plugins/anydoc/<latest>/bin/<platform>/anydoc`; `<latest>` is the notice's `latest` value or the highest version folder present; `<platform>` is `darwin-arm64`, `linux-x64` or `win32-x64`). Read `references/whats-new.md` from that folder and tell the user in one or two sentences what changed and that the host loads it fully after a restart. For the rest of this session use that folder for every command and reference; the old version folder is gone. On no: do not ask again in this session.
