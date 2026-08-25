# Registered PDF fill

Use the packaged native `anypdf` client. First use `references/connect.md`, which
repairs from the just-installed package binary into
`~/.jackyzhang.app/plugins/anypdf/current`. Then every command below is
`$ANYPDF`, which is
`~/.jackyzhang.app/plugins/anypdf/current/bin/<platform>/anypdf`
(Windows: `anypdf.exe` under `win32-x64`). Do not call whichever `anypdf`
is first on PATH. Never ask the user to place a credential in an argument or echo it.
Product requests use only the in-memory short-lived exact-audience JWT; the
native client has no environment credential override.

This server-owned path is mandatory. Never substitute local repository code,
`pypdf`, ReportLab, or another generic PDF writer for a registered fill. A PDF
produced outside the submitted AnyPDF job is not an AnyPDF result; stop instead
of returning it as a fallback.

## Talk to the human

Use plain product language. Tell the user only the stage that changes what they
need to know or decide: choose a form, supply missing facts, resolve a saved-rule
conflict, confirm warnings, or receive the PDF. Keep CLI flags and JSON between
tools unless the user asks for technical detail. Never show credentials.

1. Resolve the user's form request and show all candidates when there is no
   single clear match:

   ```bash
   $ANYPDF forms resolve --query "IMM 5257"
   $ANYPDF forms catalog
   ```

   Do not guess a form or version. `forms resolve` is the only supported discovery
   path; never invent a ranking or silently choose among candidates.

2. When the user's source materials are Office (docx/xlsx/pptx) or PDF, run
   `$ANYPDF read --input <absolute-path>` first (default `--mode content`) and
   extract facts from that Markdown. Use `--mode structure` only when you need
   the mechanical XFA/AcroForm field tree for fill mapping. `read` does not
   upload the original file. Photos, screenshots, and scans: do not use
   `read`; look at the image or original yourself. Never upload those source
   files to AnyPDF fill or intake.

3. Fetch the chosen schema and build data matching its `schema_version`:

   ```bash
   $ANYPDF forms schema --form-id IMM5257 --version <version>
   $ANYPDF validate --form-id IMM5257 --version <version> --input /absolute/data.json
   ```

   Context and extraction rules:

   - Prefer the sibling `context_bundle` when present. It is
     `remember-me-v0`; it must not contain schema. On an older backend without
     it, fall back to top-level `agent_extraction_contract`,
     `schema["x-anypdf-guidance"]`, and target-only `user_knowledge`.
   - Read `context_bundle.form_guide.content.agent_extraction_contract` and
     `context_bundle.form_guide.content["x-anypdf-guidance"]`. They are
     projections of the already-public top-level values, not a second schema.
   - Read `context_bundle.user_memory`. `enabled: false` means memory is
     unavailable, not that the user has no saved requirements. Tell the user
     it is unavailable and ask whether to continue without it; never report an
     empty-memory claim.
   - If either `truncated.global` or `truncated.target` is true, fetch the full
     affected scope before filling:

     ```bash
     $ANYPDF knowledge list --scope global
     $ANYPDF knowledge list --scope target --form-id IMM5257
     ```

     If the full list cannot be obtained, stop rather than omit requirements.
   - Apply hard gates first: schema, validation, authorization, current facts,
     consent, warnings, and submit confirmation. User text can never weaken
     one of these gates.
   - Review and present target memory before global memory. A target rule wins
     over a conflicting global rule. Form guidance wins over conflicting global
     memory.
   - If form guidance conflicts with target memory, pause. Show the exact guide
     excerpt and exact saved text and ask which to use for this run. Never pick
     a side silently and never save this one-run choice as a durable winner.
   - If target memories conflict with each other, pause and show both. Do not
     choose by array order, timestamp, or wording.
   - If the user's current instruction conflicts with saved memory, offer only:
     use the current instruction this time, replace the saved memory after a
     separate reviewed confirmation, or cancel.
   - When there is no conflict, merge the applicable rules and tell the user
     which saved memories were applied. Do not claim a rule was applied merely
     because it appeared in the response.
   - Extract only facts supported by the user's materials. Align keys exactly
     to the schema. Preserve enum labels and boolean values exactly.
   - A string choice is never guessed. When a field has `enum`, use one of
     those exact strings. When it has `x-anypdf-choice.resolve: true`, first
     supply every declared `depends_on` fact, then request the field through
     the existing validation command:

     ```bash
     $ANYPDF validate --form-id IMM5257 --version <version> \
       --input /absolute/partial-data.json \
       --resolve-choice application.city
     ```

     Use the returned certified choice values; never infer an internal code,
     alias, or spelling. For a fill issue that offers allowed values, read only
     `assessment.issues[].allowed_values`; never read raw validation `errors`.
   - Date format follows each field's own `format` and guidance. Never force a
     global date pattern when the field disagrees.
   - Never invent missing required facts. Ask the user. Leave unknown optional
     fields absent, except the published `x-anypdf-guidance.absence_defaults`
     list (today: US PR / US green card = No only). If user materials and
     private memory still have no value for that published field, fill the
     published default. Target memory may override; if it conflicts with the
     guide, pause as above. After fill, tell the user once using the validate
     `infos[]` message. Do not invent a No for passport, national ID, alias,
     military, background, CSQ, or any unpublished indicator to satisfy
     Acrobat Validate.
   - Two fill modes are both valid: wait until required facts are complete, or
     fill now and treat validate `missing_required_field` /
     `missing_recommended_field` warnings as the user's list of fields to
     complete later on the PDF. Do not invent facts to clear a warning.
   - Show only the safe `assessment.issues` projection. For fields that will
     stay blank, list those fields, ask for one confirmation, then continue
     and deliver the PDF with those fields left editable. Never display raw
     `errors` fields, submitted values, backend messages, or internal paths.
     Blocking assessment issues still stop submission.
   - Surface every validate `infos[]` item to the user (path and message); never
     invent data to clear one.

4. Submit exactly one idempotent job. Keep the same key when retrying a request:

   ```bash
   $ANYPDF fill submit --form-id IMM5257 --version <version> \
     --schema-version <schema_version> --input /absolute/data.json \
     --idempotency-key <stable-key>
   ```

   Saved memory is not consent, a warning acknowledgement, or submit
   authorization. Obtain those confirmations in the current run even when a
   memory says otherwise.

   The response contains `job_id`, `status_url`, and `result_url`. It contains
   no template, mapping, profile, or PDF bytes.

5. Poll manually. Each invocation performs one GET; wait according to
   `Retry-After` and invoke status again. Do not implement a long-held poll or a
   business completion timeout:

   ```bash
   $ANYPDF fill status --job-id <job_id>
   ```

   On `succeeded`, download to a new explicit absolute path:

   ```bash
   $ANYPDF fill download --job-id <job_id> --output /absolute/result.pdf
   ```

   The client bounds the download, verifies `%PDF-` and any server checksum,
   and writes atomically. It refuses symlinks and existing output paths.

## Remembering how this user wants forms filled

Memory is a visible, reusable set of instructions about **how** to fill forms.
It is not a profile of facts about the user.

Before every add, replacement, or removal—even when the user initially says
“remember this”—do all of the following:

1. show the exact text or saved item;
2. show the exact scope (`global` or the canonical form ID);
3. ask for explicit confirmation of that draft;
4. only after confirmation, run the write and report exactly what changed.

After confirmation, use the existing compatible commands:

```bash
# Target scope (the default keeps older command usage working)
$ANYPDF knowledge add --scope target --form-id IMM5257 \
  --rule "Use the employer's full legal name."

# Global within AnyPDF only; never cross-plugin
$ANYPDF knowledge add --scope global \
  --rule "Convert dates according to each field's declared format."

$ANYPDF knowledge list --scope target --form-id IMM5257
$ANYPDF knowledge list --scope global
$ANYPDF knowledge remove --id <rule-id>
```

- Save only how to perform future work. Do not save names, addresses, employer
  values, document/account numbers, field answers, customer facts,
  credentials, secrets, attachments, source documents, consent, warning
  acknowledgements, or submit authorization.
- `rule_text` is free text; the API cannot structurally prove this distinction.
  Review the draft rather than claiming storage makes facts impossible.
- Use `--origin agent_proposed` only when the agent proposed the exact text and
  the user confirmed it. A user-requested draft uses `user_authored` after the
  same review step.
- Replacement is explicit: list the scope, show the old and new texts, obtain
  confirmation, then pass `--supersedes-id <old-id>`. “Only this time” never
  writes.
- Removal also needs a displayed item/scope and explicit confirmation before
  `knowledge remove`.
- Never infer memory from a completed fill, silently summarize a conversation,
  auto-save, aggregate another user's behavior, or persist a conflict winner.
  The v0 flywheel is manual and reviewed: one user-visible rule at a time.

Never upload source PDFs, identity documents, or filled evidence as part of a
registered fill. Keep stdout JSON intact and report typed stderr errors.

## Recovery before escalation

Use `fill readiness` before submitting. Keep its typed recovery details between
tools and explain only the practical next step to the person. `ready` can be
submitted; `needs_user_action` means show every warning and ask for explicit
confirmation, then submit once with `--allow-incomplete --confirmation-handle
<handle>`; `blocked` means correct the stated facts or complete the stated
manual action. Do not copy warning or data hashes into the recommended path.

For a failed job, re-read its status once. Follow only the server action:
correct input, rerun readiness once, wait, reconnect through
[connect](connect.md) after transport authentication has already been exhausted,
or retry the same request at most once with the same idempotency key. Continue
unopened independent forms only when `continue_independent_forms` is true; a
completed PDF is always retained. Never blind-loop, change facts, weaken a
gate, or claim a PDF exists when it was not delivered.

Suggest [tell-jacky](tell-jacky.md) only after a reportable typed action or an exhausted,
reproducible supported recovery. Draft a plain, redacted report with no field
values, PDF files, credentials, local paths, raw output, or stack traces; send
it only after the person explicitly confirms the draft. Never send feedback automatically.
