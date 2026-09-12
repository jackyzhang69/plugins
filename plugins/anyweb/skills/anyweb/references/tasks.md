# Local tasks

Use this playbook only for a task listed by the live `commands --json` surface
and supported by the product service. Website browsing, login, and filling
stay on this computer in a visible, separate Chrome profile. AnyWeb never
takes over the user's normal Chrome. Chrome opens only after the product has
the facts it can check without a page.

## Compile first, then run

The host agent compiles one complete `local_inputs` object from the user's
files. The product then validates that pack and, only when it is ready,
logs in, clicks, and fills by itself. The host agent does **not** read a
private site graph, walk the live site to discover questions, interview the
human one field at a time, or click ordinary pages. If the first compile is
incomplete, the product returns the remaining `needs` without opening Chrome.

1. Collect the user's information files first (spreadsheet, PDF, Word, or
   other applicant records). If those files are not in the conversation yet,
   ask for the files. Do not start Chrome to go fishing.
2. Read those files once. Compile one complete `local_inputs` object for
   this site and task from those files. Use only values the files actually
   support. Do not invent answers and do not copy example placeholder keys.
3. Start the task with that object. If the result still lists `needs` and
   `runtime_active` is false, Chrome is not open: fill those items and
   resume. When the pack is ready, the executor logs in, clicks, and fills
   by itself. Keep the host command session open while the product is
   waiting, even if Chrome is not open yet.

Start by running `runtime doctor --json`. If it reports `runtime_missing`, run
the returned `runtime install --json` continuation and repeat the doctor. This
is host-agent work and does not require the human to download or move a file.
Then send the compiled request through `task start --request-stdin
--keep-runtime --json`.

Supported requests are:

```json
{
  "request": {
    "request_ref": "a caller-generated stable id",
    "site": "ircc-apr",
    "task": "first-reversible-page"
  },
  "local_inputs": {
    "replace_this_object": "compiled facts from the applicant files"
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
    "replace_this_object": "compiled facts from the applicant files"
  }
}
```

Put every bound fact in `local_inputs` before start. Do not copy an empty
object. If files are missing or the first compile still has gaps, start
anyway only after collecting the files you have; the product reports the
remaining `needs` without opening Chrome. Empty `local_inputs` is only for a
file-less probe.

Task JSON that the host should read is `result` with
`kind=anyweb_host_task_notice`. Translate `status` and `user_message`. Use
`needs` as the complete current remaining list after compile; do not invent
extra questions and do not wait to ask only the first item.

When `status` is `human_required` and `runtime_active` is false, Chrome is
not open. The product is still compiling. Fill every `needs` item from the
files and resume. Do not tell the human to open or click the website.

When `status` is `human_required` and `needs` has one or more items with
`fact_ref`:

1. Treat the list as one remaining gap, not a questionnaire. Fill every item
   from the files already collected.
2. Ask the human once for what those files still do not contain. If
   `accepted_values` or `value_type` is present, keep the answer inside that
   shape.
3. Send every collected `fact_ref` and answer together through redirected
   stdin, then run the stated continuation (`task resume`).
4. After resume, the product may return a new, shorter list only if a later
   choice opened questions that could not be known from the files. Collect
   that new list the same way: files first, one ask, one resume.

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
once, send them through redirected stdin, and the executor types and
continues by itself.

When the result says the user must complete an Authenticator or CAPTCHA in the
visible browser, tell them what to do. After they confirm they are ready, run
`task resume --task-ref <opaque ref> --json` while the retained start command
session is still active. If the prior result reported `runtime_active=false`
and no retained command session exists, add `--keep-runtime` to that resume so
a newly reached human gate stays open.

`local_inputs` and resume stdin may include dates as `YYYY-MM-DD` or
`{year,month,day}`, and repeating education or work history as arrays of
objects. Do not flatten those by hand and do not interview one field at a
time.

Use `task status`, `task result`, or `task cancel` with the opaque task ref.
Report only the plain-language state and next action. Cancellation closes the
task runtime but does not delete the user's saved local browser profile.

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
