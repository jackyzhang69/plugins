# Local tasks

Use this playbook only for a task listed by the live `commands --json` surface
and supported by the product service. AnyWeb opens a visible, separate Chrome
profile on the user's computer. It never takes over the user's normal Chrome
profile.

Start by running `runtime doctor --json`. If it reports `runtime_missing`, run
the returned `runtime install --json` continuation and repeat the doctor. This
is host-agent work and does not require the human to download or move a file.
Then send a bounded product request through `task start --request-stdin
--json`. The current supported request is:

```json
{
  "request": {
    "request_ref": "a caller-generated stable id",
    "site": "ircc-apr",
    "task": "first-reversible-page"
  }
}
```

Do not put field values, passwords, answers, tokens, cookies, or page text in
argv, environment variables, logs, feedback, or chat summaries. When the
result says human input is required, tell the user what to do in the visible
browser. After the user confirms they are ready, run `task resume --task-ref
<opaque ref> --json`. Use redirected `--input-stdin` only for typed data the
user has explicitly entrusted to the current local agent; never repeat those
values in output.

Use `task status`, `task result`, or `task cancel` with the opaque task ref.
Report only the plain-language state and next action. Cancellation closes the
task runtime but does not delete the user's saved local browser profile.

Any submit, payment, upload, signature, send, delete, removal, or withdrawal
remains outside this release. Stop before those actions even if the website
appears ready.
