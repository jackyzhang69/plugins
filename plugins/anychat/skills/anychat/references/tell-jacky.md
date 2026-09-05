# Tell Jacky (AnyChat)

## Product contract

- Feedback is optional and always draft → human review → explicit confirmation
  → send through the bundled AnyChat CLI.
- Never call a service endpoint directly or describe AnyChat's private feedback
  transport, authentication, storage, or operator workflow.
- Never send chat text, contact or group identity, credentials, local paths,
  process identifiers, attachment bytes, or private implementation details.

## Draft and confirmation

Prepare a short draft with:

1. type: `feature-request` or `bug-report`;
2. title and description in the human's own language;
3. the automatically collected safe environment facts that help reproduce a
   bug: exact operating-system version/build and architecture, AnyChat and
   relevant app/runtime builds, host/restricted execution views, permission
   action and result, stage verdicts and support codes, and content-free counts;
4. each optional screenshot the human wants to include.

Run `feedback preview` first. Show the human the complete draft and summarize
the safe machine context in plain language; make the exact preview available
without dumping raw JSON into the normal reply. Wait for explicit approval,
then use `feedback create --user-confirmed --confirmation-binding <binding>`
with the same draft and the opaque one-time binding from that exact preview.
Never reuse a binding or rebuild the draft from memory. For non-ASCII text on
Windows, use the CLI's UTF-8 draft-file input rather than placing the text on
argv. AnyChat independently gathers, validates, and scrubs the context.

Do not ask the human for facts the host agent can inspect. In particular, never
ask for a path, raw log, process id, terminal output, password, chat content, or
contact name. If a safe environment fact is missing, the host agent inspects it
and supplies a typed claim when requested; AnyChat validates the claim or leaves
the fact unknown.

## Replies

Once per authenticated host-agent session, `feedback inbox --json` may check for
unread replies. Show each reply in plain language; only after the human has seen
it, mark that reply read with the exact CLI continuation. Inbox failure never
blocks the user's core AnyChat request.

## Result and failure

- After a confirmed send, report the feedback id and whether Jacky received it
  or it was saved only on this computer.
- A local record is not proof of delivery. If delivery failed or is unknown,
  say so plainly and do not promise a fix date.
- If login expired, reconnect through [connect](connect.md). For other failures,
  give only the product-level reason and actionable next step.

## Proactive offer

Offer “要不要告诉 Jacky？” only when AnyChat reports `blocked` with
`offer_tell_jacky=true`. Never auto-send and never offer it while an agent or
human continuation still exists.
