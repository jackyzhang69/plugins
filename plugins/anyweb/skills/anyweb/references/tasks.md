# Local tasks

Use this playbook only for a task listed by the live `commands --json` surface
and supported by the product service. AnyWeb opens a visible, separate Chrome
profile on the user's computer. It never takes over the user's normal Chrome
profile. All website browsing, login, and filling happen on this computer.

Start by running `runtime doctor --json`. If it reports `runtime_missing`, run
the returned `runtime install --json` continuation and repeat the doctor. This
is host-agent work and does not require the human to download or move a file.
Then send a bounded product request through `task start --request-stdin
--keep-runtime --json`. The command intentionally remains active while a
visible browser is waiting for human input; retain the host command session
instead of terminating it. Supported requests are:

```json
{
  "request": {
    "request_ref": "a caller-generated stable id",
    "site": "ircc-apr",
    "task": "first-reversible-page"
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
  }
}
```

Task JSON that the host should read is `result` with
`kind=anyweb_host_task_notice`. Translate `status` and `user_message`. Use
`needs` as the complete current list; do not invent extra questions and do not
wait to ask only the first item.

When `status` is `human_required` and `needs` has one or more items with
`fact_ref`:

1. For every item, first look in the customer material the user already
   supplied.
2. If a fact is still missing, ask the human the plain-language `label`. If
   `accepted_values` or `value_type` is present, keep the answer inside that
   shape.
3. Send every collected `fact_ref` and answer together through redirected
   stdin, then run the stated continuation (`task resume`).
4. After resume, the product may ask a new, shorter list if a later choice
   opened more questions. Collect that new list the same way.

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
