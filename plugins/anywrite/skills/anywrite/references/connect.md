# Connect, readiness, errors, feedback

## Sign-in

First check `anywrite doctor --json`. It reads only: whether the shared sign-in is present, whether the service answers, whether access is allowed, and whether the saved format matches. If the shared sign-in is already connected, do not ask for another token.

When a token is needed, prefer a file that holds it and pipe the file to `anywrite login --token-stdin`. A chat paste is allowed after warning that a file is safer; pipe it without echoing it back. Never put a token in arguments, logs, screenshots, output, or text you show. Never send the human to a terminal.

The check happens before anything is stored: the shared Jacky Zhang sign-in is saved only after the service accepts it for AnyWrite. AnyWrite does not create its own token file. `anywrite whoami` shows which account is connected (masked). Use `anywrite logout` only when the user asks to disconnect: it removes the shared sign-in that the other Jacky Zhang plugins use too.

## Readiness result

A command that needs the service returns a typed readiness result when nobody is signed in, instead of a bare error. Tell the person the `say_to_user` text in ordinary words, wait until they say it is done, then run the exact `continue_args` again. Do not retry before that. Treat it as the continuation: satisfy the stated need (reuse a governed credential if one exists; otherwise obtain the sign-in), run `anywrite login --token-stdin`, then run the exact `continue_args` again. File paths in `continue_args` appear as `-`; supply the same content on standard input. Do not ask the human to repeat their request and do not invent flags. `commands`, `doctor`, `definitions validate`, `login` and `logout` never wait for sign-in.

## What the error codes mean for the user

- `not_authorized`, `invalid_token`: the sign-in is not accepted; sign in again.
- `anywrite_disabled`: AnyWrite is not switched on for this account yet. Say so; do not retry.
- `insufficient_scope`, `forbidden`: this account may not do that. Do not retry.
- `service_unavailable`, `network_unavailable`: the service could not be reached or answered badly. This is not "nothing found". Try once more, then tell the user plainly.
- `definition_not_found`: that method does not exist for this account.
- `payload_too_large`, `definition_invalid`, `invalid_request`: the draft or request must change.
- Everything else: report it in plain words using the message and next step in the answer.

## Tell Jacky (feedback)

Prepare a JSON object with only `type` (`bug-report`, `feature-request` or `knowledge-tip`), `title`, `description` and `idempotency_key`. Keep `description` short and about the product issue (under 4000 characters). Do not include saved methods, example text, search results, credentials, file paths or customer information, and do not paste a chat transcript; the command refuses transcript-shaped text and does not send it.

Run `anywrite feedback --input <file or ->` to show the draft without sending. Show that exact draft to the person and get a yes. Then repeat with `--user-confirmed`. Delivery may go through the shared account or stay as a copy on this computer; tell the person which, in ordinary words. `anywrite feedback-list` and `anywrite feedback-status --id <id>` read earlier reports. If delivery times out, check the list before sending the same confirmed draft again with the same `idempotency_key`.
