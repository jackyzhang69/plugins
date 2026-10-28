# Connect, readiness, errors, feedback

## Sign-in

First check `anywrite doctor --json`. It reads only: whether the shared sign-in is present, whether the service answers, whether access is allowed, and whether the saved format matches. If the shared sign-in is already connected, do not ask for another token.

When a token is needed, prefer a file that holds it and pipe the file to `anywrite login --token-stdin`. A chat paste is allowed after warning that a file is safer; pipe it without echoing it back. Never put a token in arguments, logs, screenshots, output, or text you show. Never send the human to a terminal.

The check happens before anything is stored: the shared Jacky Zhang sign-in is saved only after the service accepts it for AnyWrite. AnyWrite does not create its own token file. `anywrite whoami` shows which account is connected (masked). Use `anywrite logout` only when the user asks to disconnect: it removes the shared sign-in that the other Jacky Zhang plugins use too.

## This computer (one computer on the free plan)

AnyWrite works only on a computer that is approved for the account. The free plan includes one computer. The first time AnyWrite is used on a computer, it approves that computer by itself, using a private key kept in the Mac Keychain or Windows Credential Manager. Nothing is saved in a file, so copying the program, the sign-in or a saved file to another computer does not give that computer access. Do not ask the user for anything, and never try to read or move the key.

- `anywrite device status --json` shows whether this computer is approved (`bound` or `unbound`) and its name. It changes nothing.
- To move to a new computer: install AnyWrite on the new computer and sign in there. If the account has no free slot, the new computer is told the limit is reached. Free the old slot first, either by running `anywrite device remove-this` on the old computer, or by removing it in the Portal under Account, then Devices. After that, the new computer approves itself the next time AnyWrite runs. Saved methods stay with the account and are not deleted.
- `anywrite device remove-this` frees this computer's slot. Run it without `--confirmed` to show what it does, tell the person in ordinary words, and only after a yes repeat it with `--confirmed`. Do not use AnyWrite on that computer afterwards unless the person wants it to take a slot again (any later command approves it again if a slot is free).
- `anywrite device reset-key` is for a computer that was removed in the account page (Portal, Account, Devices): the service then refuses that computer for good, so it needs a new identity. Run it without `--confirmed` to explain (no network), and only after the person says yes, repeat it with `--confirmed`. It sets the computer up again and takes one free computer slot; if there is none, it returns the `device_limit_reached` result. A computer that is still approved is left unchanged.
- AnyWrite never removes another computer by itself. Only the person can do that in the Portal.

## Readiness result

A command that needs the service returns a typed readiness result when nobody is signed in, instead of a bare error. The same happens when the person must act on this computer: `device_limit_reached` (status `needs_human`, remove the old computer in the Portal under Account, then Devices) `device_removed` (this computer was removed in the account page; ask whether they want to use it again, and only after a yes run `anywrite device reset-key --confirmed`, then the exact `continue_args`), and `device_key_unavailable` (unlock or repair the Keychain or Credential Manager). Tell the person the `say_to_user` text in ordinary words, wait until they say it is done, then run the exact `continue_args` again. Do not retry before that. Treat it as the continuation: satisfy the stated need (reuse a governed credential if one exists; otherwise obtain the sign-in), run `anywrite login --token-stdin`, then run the exact `continue_args` again. File paths in `continue_args` appear as `-`; supply the same content on standard input. Do not ask the human to repeat their request and do not invent flags. `commands`, `doctor`, `definitions validate`, `login` and `logout` never wait for sign-in.

## What the error codes mean for the user

- `not_authorized`, `invalid_token`: the sign-in is not accepted; sign in again.
- `anywrite_disabled`: AnyWrite is not switched on for this account yet. Say so; do not retry.
- `insufficient_scope`, `forbidden`: this account may not do that. Do not retry.
- `device_limit_reached`: the account's computer slots are all used (one on the free plan). Do not retry and do not remove anything yourself. Tell the user in plain words to remove the old computer in the Portal under Account, then Devices (or run `anywrite device remove-this` on the old computer), then run the command again.
- `device_removed`: this computer was removed in the account page. Do not retry. Tell the user in plain words, and if they want to use this computer again, run `anywrite device reset-key --confirmed` after their yes. It needs one free computer slot.
- `device_unapproved`: this computer is not approved (for example it was removed in the Portal). Run the command once more; it approves itself if a slot is free. If it then reports `device_limit_reached`, follow that advice.
- `device_key_unavailable`: this computer's Keychain or Credential Manager cannot be used. Tell the user to unlock or repair it for their user account and try again. Do not look for another way to store the key.
- `invalid_dpop_proof`: the service did not accept this computer's proof. Usually the computer's date and time are wrong. Tell the user to fix the clock, then run once more. Do not loop.
- `service_unavailable`, `network_unavailable`: the service could not be reached or answered badly. This is not "nothing found". Try once more, then tell the user plainly.
- `definition_not_found`: that method does not exist for this account.
- `payload_too_large`, `definition_invalid`, `invalid_request`: the draft or request must change.
- Everything else: report it in plain words using the message and next step in the answer.

## Tell Jacky (feedback)

Prepare a JSON object with only `type` (`bug-report`, `feature-request` or `knowledge-tip`), `title`, `description` and `idempotency_key`. Keep `description` short and about the product issue (under 4000 characters). Do not include saved methods, example text, search results, credentials, file paths or customer information, and do not paste a chat transcript; the command refuses transcript-shaped text and does not send it.

Run `anywrite feedback --input <file or ->` to show the draft without sending. Show that exact draft to the person and get a yes. Then repeat with `--user-confirmed`. Delivery may go through the shared account or stay as a copy on this computer; tell the person which, in ordinary words. `anywrite feedback-list` and `anywrite feedback-status --id <id>` read earlier reports. If delivery times out, check the list before sending the same confirmed draft again with the same `idempotency_key`.
