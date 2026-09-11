# Local tasks

Use this playbook only for a task listed by the live `commands --json` surface
and supported by the product service. AnyWeb opens a visible, separate Chrome
profile on the user's computer. It never takes over the user's normal Chrome
profile. All website browsing, login, and filling happen on this computer.

## Compile first, then run

The website already declares what this fill needs. The host agent does **not**
walk the live site to discover questions, and it does **not** interview the
human one field at a time.

1. Collect the user's information files first (spreadsheet, PDF, Word, or
   other applicant records). If those files are not in the conversation yet,
   ask for the files. Do not start Chrome to go fishing.
2. Read those files once. Build one complete `local_inputs` object for this
   site and task. Use only values the files actually support. Do not invent
   answers.
3. Start the task with that object. AnyWeb compiles it into the run and
   executes. Keep the host command session open while a visible browser is
   waiting for a person.

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
  "local_inputs": {}
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
  "local_inputs": {}
}
```

Put every bound fact in `local_inputs` before start. Empty `local_inputs` is
only for a file-less probe; it is not the normal fill.

Task JSON that the host should read is `result` with
`kind=anyweb_host_task_notice`. Translate `status` and `user_message`. Use
`needs` as the complete current remaining list after compile; do not invent
extra questions and do not wait to ask only the first item.

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

When `status` is `human_required` and a `needs` item has no `fact_ref`, that
item is browser work for the current host agent. Navigate the visible browser
and use already authorized credential facilities without asking the user to
click ordinary login pages. Only a real Authenticator, CAPTCHA, passcode, or
equivalent live challenge is `needs_human`; ask the user to complete that
single action, then continue the same login and resume the same task.

When the result says the user must act in the visible browser, tell them what
to do. After they confirm they are ready, run `task resume --task-ref
<opaque ref> --json` while the retained start command session is still active.
If the prior result reported `runtime_active=false` and no retained command
session exists, add `--keep-runtime` to that resume so a newly reached human
gate stays open.

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
