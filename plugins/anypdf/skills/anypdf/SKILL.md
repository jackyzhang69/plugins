---
name: anypdf
description: >-
  READ THIS FIRST for AnyPDF. Fill a registered PDF form, request a missing
  form two ways (Tell Jacky text, or direct form request with an official blank
  PDF), sign IMM5476 with supplied signature images and export a scanned PDF,
  connect / login --token-stdin. Answer from the live CLI, never from
  memory of an older release or a frozen form list. One discovery file;
  playbooks in references/. Ask `anypdf commands --json`.
when_to_use: |-
  Load on plugin start. "what can AnyPDF do", "which forms", "fill a registered form",
  "fill a registered PDF", "submit a PDF template", "form request", "add this form",
  "connect / log in", "tell Jacky", "sign IMM5476", "signed scanned PDF".
---

# AnyPDF — what this plugin can do

Load this on plugin start, and again when the user asks what AnyPDF can do,
which forms it fills, or how to use it.

## Talk to the human

Speak in everyday words. Say what you can do and what happens next. Do not
lead with binary paths, `--help`, or raw JSON. Keep CLI and JSON between
tools. Never show credentials. On the first session after install, read
[references/get-started.md](references/get-started.md) before your first
user-visible reply. After a version update, read
[references/whats-new.md](references/whats-new.md) and resume the prior intent
without asking the human to repeat it. Never ask the human to run doctor as
homework. Run the intended ordinary command first; when stdout is
`jz.plugin.envelope.v1`, follow its exact `status` and `continue_args` until
the product reports `ready`, then resume the sealed request.

## When the user asks "what can you do?"

Use this as a runtime answer harness. The answer is valid only after live
discovery; do not fill it from this file's memory, a previous chat, or a
handwritten form list.

1. If the client is not connected, follow [connect](references/connect.md),
   then run the live client:

```bash
$ANYPDF doctor --json
$ANYPDF forms catalog --json
```

2. Treat the returned values as the current facts. `doctor` establishes that
   this installed client is ready; `forms catalog` supplies the current
   published entries that may be offered for filling. Do not add an entry,
   count, category, example, or promise that is not supported by those values.

3. Organize a short product answer from the returned entries only:

   - Describe published entries as available for filling only when the live
     catalog returned them. Keep any names or identifiers tied to those
     returned records.
   - Treat an issuing-authority official blank PDF that is not in the catalog
     as recognition or support-request input only. It is not immediately
     fillable, and the answer must not promise that it will become fillable
     right away.
   - Explain that a fill uses the user's confirmed information. Ask only for
     a missing fact or a decision that the current request actually needs;
     otherwise do not ask for extra information.
   - Say that AnyPDF does not guess missing facts and does not submit an
     application or other filing to an issuing authority on the user's behalf.
     A filled IRCC PDF that still needs Acrobat Validate is not ready to upload.
     A file that looks blank in Preview is not empty if Adobe Acrobat shows the values.

4. Translate the result into everyday product language. Keep CLI names, JSON,
   paths, credentials, raw errors, support/debug fields, and implementation
   details between tools; none of them belongs in the human-facing answer. If
   live discovery cannot verify availability, say so without inventing a
   capability.

## Agent router — intents

| User intent | Host does | Human may be asked |
|---|---|---|
| "what can AnyPDF do / which forms" | Live `$ANYPDF forms catalog --json` + `$ANYPDF doctor --json`; translate to product language | Connect once if not logged in ([connect](references/connect.md)) |
| Connect / login | [connect](references/connect.md): pipe Portal token via stdin | Token file path or one-time paste (never argv) |
| Fill a registered PDF | [fill](references/fill.md): resolve form, gather facts, submit fill job | Facts for fields; confirm before irreversible steps |
| Sign a filled IMM5476 / export a signed scanned PDF | [fill](references/fill.md#sign-a-completed-imm5476): use the user's succeeded fill and supplied signature images | Explicit signer/image/date review and approval before signing |
| Ask to add a missing form (text only / Tell Jacky) | [tell-jacky](references/tell-jacky.md): confirmed product feedback; no PDF bytes | Confirm the exact draft before send |
| Direct form request (official blank PDF present) | [intake](references/intake.md): one PDF upload | Confirm the exact blank PDF is the issuing-authority official form |
| Feature / bug / tip for Jacky | [tell-jacky](references/tell-jacky.md) | Confirm the exact draft before send |

### New-form ask routing (two valid user paths)

Users may request a missing form either way. Do not collapse one into the other.

1. **Tell Jacky (text):** the user asks to support a form by name/description and
   has no issuing-authority official blank PDF. Route to
   [tell-jacky](references/tell-jacky.md). Never invent or download a blank to
   force intake.
2. **Direct form request (intake):** the user provides (or points at) an
   issuing-authority official blank PDF. Route to
   [intake](references/intake.md). Prefer intake over Tell Jacky when that blank
   is present; Tell Jacky must not carry PDF bytes.
3. After intake `known_exact`, continue [fill](references/fill.md). After
   `new_source`, record `source_sha256` and stop promising fillability. Wrong
   upload deletion still uses Tell Jacky with the exact returned
   `source_sha256`.

Playbooks: [connect](references/connect.md), [fill](references/fill.md),
[intake](references/intake.md), [tell-jacky](references/tell-jacky.md).

## Every command, one line each

Run each entry below as `$ANYPDF <command words and flags>` (see
[connect](references/connect.md) for `$ANYPDF`); the entries show only the
command words and their key flags. Every command prints JSON except `read`.
Ask the live client for every argument of every command: `"$ANYPDF" commands --json`.

- `login --token-stdin` saves the access key piped on stdin, after proving it works.
- `whoami` shows which account this machine is signed in as.
- `logout` removes the saved access key from this machine.
- `doctor` checks, offline, that this machine is set up; `doctor --repair-install` installs this package as the only copy.
- `read --input <absolute path> --mode content|structure` turns a local Office or PDF file into Markdown without uploading it.
- `forms catalog` lists the forms available now; `forms resolve --query <text>` finds a form by name or number.
- `forms schema --form-id <id>` shows what a form asks for, with its schema version and guidance.
- `validate --form-id <id> --input <answers.json>` checks answers before anything is filled; add `--resolve-choice <field>` for choice fields.
- `complete start --form-id <id> --input <answers.json>` returns a short form for the missing answers.
- `complete apply --session-id <id> --values <values.json>` hands back what was typed in chat.
- `complete result --session-id <id> --wait <seconds>` collects what was sent from the browser page.
- `fill readiness --form-id <id> --input <answers.json>` checks whether answers can be filled now without creating a job.
- `fill submit --form-id <id> --input <answers.json> --idempotency-key <key>` asks for the interactive filled PDF; `--allow-incomplete` fills with confirmed blanks.
- `fill status --job-id <id>` checks whether the fill is ready.
- `fill download --job-id <id> --output <absolute path>` saves the interactive filled PDF; open it in Adobe Acrobat or Adobe Reader.
- `signatures create --job-id <id> --input <context.json>` starts signing a completed IMM5476.
- `signatures status --session-id <id>` checks a signing session.
- `signatures add-asset --session-id <id> --image <path>` uploads a signature image.
- `signatures assign --session-id <id> --input <assignment.json>` matches an image to a signer and date.
- `signatures preview --session-id <id> --expected-version <n>` shows every signer, image and date for the human to approve.
- `signatures confirm --session-id <id> --input <confirmation.json> --user-confirmed` signs after the human approved the preview.
- `signatures download --session-id <id> --output <absolute path>` saves the signed PDF.
- `intake submit --pdf <file.pdf> --idempotency-key <key>` sends an issuing-authority official blank PDF so a missing form can be recognised.
- `knowledge list --scope target|global` shows saved filling rules.
- `knowledge add --rule <text>` saves one filling rule.
- `knowledge remove --id <id>` deletes one filling rule.
- `feedback submit --report <report.json>` sends a Tell Jacky note.
- `feedback status --report-id <id>` checks on a note that was sent.
- `resume --envelope-stdin` continues the saved request behind a `needs_agent` response piped on stdin.
- `commands` prints this command surface as JSON.

## Live CLI discovery (fail-closed)

Ask the live CLI, never from memory of an older release. Volatile form catalogs
are not dumped here.

```bash
"$ANYPDF" commands --json
```

After a marketplace install or update, [connect](references/connect.md) repairs from this
package into `~/.jackyzhang.app/plugins/anypdf/current`. Later agent commands
use `$ANYPDF` at that `current` binary, not whichever `anypdf` is first on PATH.

## Recovery before escalation

Keep typed recovery details between tools and explain the next choice in plain
language. Follow the bounded recovery procedure in [fill](references/fill.md) before
suggesting [tell-jacky](references/tell-jacky.md). Never send feedback automatically.

## Updates

`anypdf` may print one stderr line with schema `jz.plugin.update_notice.v1`. It never blocks a command. Finish what the user asked first, then tell them once, in plain words, that a newer version is available and ask whether to update now. On yes: refresh this plugin through the host (Claude Code: `claude plugin marketplace update jacky-plugins`, then `claude plugin update anypdf@jacky-plugins`; Codex: `codex plugin marketplace upgrade jacky-plugins`, then `codex plugin add anypdf@jacky-plugins`; other hosts: attach the new `plugins/anypdf/` tree). Then run `doctor --repair-install` with the binary from the **new** package, not the folder this session started from (Claude Code: `~/.claude/plugins/cache/jacky-plugins/anypdf/<latest>/bin/<platform>/anypdf`; Codex: `~/.codex/plugins/cache/jacky-plugins/anypdf/<latest>/bin/<platform>/anypdf`; `<latest>` is the notice's `latest` value or the highest version folder present; `<platform>` is `darwin-arm64`, `linux-x64` or `win32-x64`). Read `references/whats-new.md` from that folder and tell the user in one or two sentences what changed and that the host loads it fully after a restart. For the rest of this session use that folder for every command and reference; the old version folder is gone. On no: do not ask again in this session.
