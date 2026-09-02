# Local tasks

Use this playbook only for a task listed by the live `commands --json` surface
and supported by the product service. AnyWeb opens a visible, separate Chrome
profile on the user's computer. It never takes over the user's normal Chrome
profile.

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

AnyWeb stores only dictionary-validated, machine-fillable answers encrypted in
Portal, scoped to the signed Portal user, signed Portal account, and site. A
later run for that exact scope reuses valid answers and asks only for missing
facts. Answers are never shared with another user or account, and
authentication challenges remain in their separate security-answer store.

The EE task inspects the signed task scope after the user signs in. With no
matching draft it creates one draft at most once. With exactly one matching
draft it continues that draft. With multiple or non-adoptable matches it stops
without choosing or creating anything.

Do not put field values, passwords, answers, tokens, cookies, or page text in
argv, environment variables, logs, feedback, or chat summaries. When the
result says human input is required, tell the user what to do in the visible
browser. After the user confirms they are ready, run `task resume --task-ref
<opaque ref> --json` while the retained start command session is still active.
If the prior result reported `runtime_active=false` and no retained command
session exists, add `--keep-runtime` to that resume so a newly reached human
gate stays open. Use redirected `--input-stdin` only for typed data the user has
explicitly entrusted to the current local agent; never repeat those values in
output.

When a result contains one `needs_agent` requirement with an opaque `field_ref`,
first look for that fact in the customer material the user already supplied. If
it is absent, ask the plain-language question in `label`. Send only the returned
opaque `field_ref` and answer through redirected stdin, then invoke the stated
continuation. Do not reveal or reinterpret opaque field refs. The CLI sends
answers only through the protected authenticated answer route, then resolves
the complete encrypted Portal answer set for the current run; values never
belong in argv or output.

A `needs_agent` requirement without `field_ref` is browser work owned by the
current host agent. Navigate the visible browser and use already authorized
credential facilities without asking the user to click ordinary login pages.
Only a real Authenticator, CAPTCHA, passcode, or equivalent live challenge is
`needs_human`; ask the user to complete that single action, then continue the
same login and resume the same opaque task ref.

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
