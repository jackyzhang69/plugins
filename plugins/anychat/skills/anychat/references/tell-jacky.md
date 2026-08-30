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

1. type: `feature-request`, `bug-report`, or `knowledge-tip`;
2. title and description in the human's own language;
3. safe environment facts that help reproduce a bug: operating system and
   version/build, architecture, AnyChat version, chat-app version when known,
   permission action and result, one product support code, and reproduction
   count;
4. each optional screenshot the human wants to include.

Show the complete draft and safe context before sending. Wait for explicit
approval, then use `feedback create --user-confirmed`. For Chinese text on
Windows, use the CLI's UTF-8 draft-file input rather than placing the text on
argv. AnyChat validates and scrubs the submitted context.

Do not ask the human for facts the host agent can inspect. In particular, never
ask for a path, raw log, process id, terminal output, password, chat content, or
contact name. If a safe environment fact is missing, the host agent obtains it
or leaves it unknown.

## Replies

Once per authenticated host-agent session, `feedback inbox --json` may check for
unread replies. Show each reply in plain language; only after the human has seen
it, mark that reply read with the exact CLI continuation. Inbox failure never
blocks the user's core AnyChat request.

## Result and failure

- After a confirmed send, report the feedback id and whether delivery succeeded.
- A local record is not proof of delivery. If delivery failed or is unknown,
  say so plainly and do not promise a fix date.
- If login expired, reconnect through [connect](connect.md). For other failures,
  give only the product-level reason and actionable next step.

## Proactive offer

Offer “要不要告诉 Jacky？” only when AnyChat reports `blocked` with
`offer_tell_jacky=true`. Never auto-send and never offer it while an agent or
human continuation still exists.
