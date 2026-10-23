# Local tasks

Use this playbook only for a task listed by the live `commands --json` surface
and supported by the product service. Website browsing, login, and filling
stay on this computer in a visible, separate Chrome profile. AnyWeb never
takes over the user's normal Chrome. Chrome opens only after the product has
the facts it can check without a page.

## Compile first, then run

This is the published user path, not a crawl. The host agent only compiles
the applicant pack and handles product-reported errors. Login, passcode,
security questions, clicks, and filling are executor work.

1. Collect the user's information files first (spreadsheet, PDF, Word, or
   other applicant records). If those files are not in the conversation yet,
   ask for the files. Do not start Chrome to go fishing.
2. Run the installed `anyweb-catalog requirements --pin <task pin>`. That
   response is the production authority for the published release. This plugin
   does not carry a site catalog or a fact schema. Compile one complete `local_inputs` object keyed by each
   field's `fact_ref`. Use only values the files actually support. Dates may
   be `YYYY-MM-DD` or `{year,month,day}`. Education and work history may be
   arrays of objects. Do not invent answers, do not copy example placeholder
   keys, and do not use private site field names as keys.
3. Start the task with that object. The product preflights without opening Chrome.
   Treat preflight outcomes in product language: wrong/incomplete pack → fix pack
   or ask once for missing facts; business end state → explain and stop (not a
   successful fill); graph insufficient → stop this fill (repair is a separate
   product path, not something you invent here); ready → only then may Chrome
   open for the supported fill. If materials or the published schema change,
   compile and preflight again — do not reuse an earlier ready result.
4. If the result lists `needs` with `obtain=host_self_correct`, the JSON
   does not match the schema. Fix those items yourself from the files and
   resume. Do not stop silent, and do not ask the human to retype a value
   the files already contain.
5. If `needs` are missing facts (`obtain=user_materials_first`) and
   `runtime_active` is false, Chrome is still closed: ask once for what the
   files cannot supply, then resume. Batch known asks; do not re-ask facts
   already supplied from the files.
6. When the pack is ready, the executor uses the Portal identity in
   `~/.jackyzhang.app/token/user.json` to look up the saved website login
   and security answers for this user. If a login is missing, ask for a
   local file and save it; if the human already pasted the secret in chat,
   warn once and continue. Email or authenticator codes pause in this
   conversation; the executor types them. If that code does not take, stay on
   the same Chrome page: the executor clicks Resend once and asks for the new
   email code. Never start a second login, never kill Chrome, and never begin
   a new task to retry a passcode. Saved security answers are reused
   from the database; new ones are asked once, typed by the executor, and
   stored only after the website accepts them. The human does not touch the
   browser except for Authenticator or CAPTCHA. After start, poll and resume
   through MCP (`step_status` / `task_resume`). If `runtime_active` is true,
   Chrome stays with the local runtime until resume, timeout, or cancel. Do
   not shell `task status` / `task resume` as a hang-restart host loop.
   Login, passcode, and security questions are a fixed module the
   product attaches in front of the fill; do not invent a second login
   flow.

Start the fill with `task start --request-stdin --json`. If stdout is a
`jz.plugin.envelope.v1` `needs_agent` for runtime or connect, follow
`continue_args` until `ready`, then resume the same start. If it is
`needs_human` asking to install Google Chrome, tell the human that one
sentence, then resume the same start after they confirm Chrome is installed.
Do not ask the human to install a runtime or run doctor.

Supported requests are:

```json
{
  "request": {
    "request_ref": "a caller-generated stable id",
    "site": "ircc-apr",
    "task": "first-reversible-page"
  },
  "local_inputs": {
    "input:<64-hex from the live product schema>": "value taken from the applicant files"
  }
}
```

For Express Entry profile creation or continuation through the pre-submit
boundary:

```json
{
  "request": {
    "request_ref": "a caller-generated stable id",
    "site": "ircc-ee-profile",
    "task": "fill-to-pre-submit"
  },
  "local_inputs": {
    "input:<64-hex from the live requirements response>": "value taken from the applicant files"
  }
}
```

Put every bound fact in `local_inputs` before start. Keys must be the
schema `fact_ref` values. Do not copy an empty object. If files are missing
or the first compile still has gaps, start anyway only after collecting the
files you have; the product reports the remaining `needs` without opening Chrome. Empty
`local_inputs` is only for a file-less probe.

Task JSON that the host should read is `result` with
`kind=anyweb_host_task_notice`. Translate `status` and `user_message`. Use
`needs` as the complete current remaining list after compile; do not invent
extra questions and do not wait to ask only the first item.

When `status` is `human_required` and `runtime_active` is false, Chrome is
not open. The product is still compiling. Fill every `needs` item from the
files and resume. Do not tell the human to open or click the website.

When `status` is `human_required` and `needs` has one or more items with
`fact_ref`:

1. Treat the list as one remaining gap, not a questionnaire.
2. If `obtain` is `host_self_correct`, fix those JSON items yourself from
   the files. Do not stop silent.
3. For the remaining items, fill every value from the files already
   collected. Ask the human once for what those files still do not contain.
   If `accepted_values` or `value_type` is present, keep the answer inside
   that shape.
4. Send every collected `fact_ref` through MCP `task_resume` as
   `fact_ref → secret_ref` file paths. Do not paste secrets into the tool
   call or chat.
5. After resume, the product may return a new, shorter list only if a later
   choice opened questions that could not be known from the files. Collect
   that new list the same way: files first, self-correct first, one ask, one
   resume.

Do not put field values, passwords, answers, tokens, cookies, or page text in
argv, environment variables, logs, feedback, or chat summaries. Use redirected
`--input-stdin` only for typed data the user has explicitly entrusted to the
current local agent; never repeat those values in output.

Ordinary Sign In, Continue, I Accept, login, and form filling are executor
work after a complete compile. Do not ask the human to click those, and do
not click them yourself in the visible browser.

When `status` is `human_required` and a `needs` item has no `fact_ref`, that
item is only a real Authenticator, CAPTCHA, or equivalent in-window
challenge. Email one-time codes and static security answers that are not
already saved must arrive as `fact_ref` items; collect them from the human
once, send them through MCP `task_resume` (`secret_ref`), and the executor
types and continues by itself. If the product says the passcode did not
take, ask for the new email code on that same task. Do not `task start`
again.

When the result says the user must complete an Authenticator or CAPTCHA in the
visible browser, tell them what to do. After they confirm they are ready, call
MCP `task_resume` for that `task_ref`. Do not start a second CLI process to
restart the wait.

`local_inputs` and resume stdin may include dates as `YYYY-MM-DD` or
`{year,month,day}`, and repeating education or work history as arrays of
objects. Do not flatten those by hand and do not interview one field at a
time.

The supported host loop is MCP (`task_status` / `step_status` /
`task_resume` / `credential_cache_*`). The one-liner is
`codex mcp add anyweb -- anyweb mcp serve`. Tool results never include
passwords, passcodes, or security answers. If a security-question `need`
shows `hints.last_answer_incorrect`, ask for a different answer; if
`cache_outcome=invalidated`, do not reuse the previous saved answer. A
passcode need may include `hints.resent` but the spoken prompt stays the
same first-time sentence. Skill→CLI `task status` / `task resume`
hang-restart is not a supported host path. Do not document or use CLI as
a fallback when MCP is missing — install MCP instead.

For an explicit same-page email-code resend, call MCP `task_resume` with the current `task_ref`, `action="request_passcode_resend"`, and `expected_passcode_epoch` set to the notice's `hints.passcode_epoch`, without `inputs`; duplicate or stale epochs are rejected before any click.

Use MCP `step_status` for the current wait. `task result` and `task cancel`
remain one-shot CLI commands with the opaque task ref, not a status/resume
loop. Report only the plain-language state and next action. Cancellation
closes the task runtime but does not delete the user's saved local browser
profile.

To delete the current Portal user's reusable EE answers for this site, obtain
explicit user confirmation and send this object through protected stdin to `task
forget-answers --input-stdin --json`:

```json
{
  "site": "ircc-ee-profile",
  "user_confirmed": true
}
```

Do not claim that forgetting answers deletes a browser profile, task record,
website draft, credentials, or security-question answers.

Any final Continue/submit, payment, upload, signature, send, delete, removal,
or withdrawal remains outside this release. Stop before those actions even if
the website appears ready.
