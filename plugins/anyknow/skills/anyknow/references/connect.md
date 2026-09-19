# Connect

First check `anyknow status`. If the shared user slot is already connected, do not ask for another token.
When local login and configuration exist, status checks current authorization and service storage online.
A missing login/configuration, rejected authorization or unavailable service is not an empty knowledge library.

When a token is needed, prefer a file containing it and pipe the file to `anyknow login --token-stdin`. A chat paste is allowed after warning that a file is safer; pipe it without echoing it back. Never place a token in argv, logs, screenshots, JSON output, or skill text, and never send the human to a terminal.

The CLI validates through accountd before writing the canonical shared `token/user.json`. It does not create a product token file.

Use `anyknow commands --json` to discover the packaged commands and `anyknow whoami` to verify the connected account (the subject is masked). Use `anyknow logout` only when the user asks to disconnect: it removes the shared user login used by the other plugins too.

An ordinary verb that needs the shared connection (search, get, links, versions,
operation, browse, topics, export, commit-import, feedback, record-reuse, pair) returns the
typed readiness envelope `jz.plugin.envelope.v1` instead of a bare error when no credential is
present. Treat that envelope as the continuation contract: satisfy the declared need (reuse a
governed credential if it exists; otherwise obtain the sign-in input), run `anyknow login
--token-stdin`, then re-invoke the envelope's exact `continue_args`. Path-valued arguments in
`continue_args` appear as `-`; supply the same content on stdin for that argument, because the
contract forbids filesystem paths on argv. A verb's own local validation still runs first, so a
changed preview fails as `preview_changed` before any readiness question. Do not ask the human to
repeat the original request, and do not invent flags. The local-only verbs (`commands`,
`prepare`, `prepare-import`, `login`, `logout`) never gate on the connection.
