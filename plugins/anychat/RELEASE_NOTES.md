# AnyChat 0.1.31

## User-visible changes

- Follow a named topic (a person or a group, plus what you care about). New messages are found on this computer; the topic and who-is-who live with the account so another computer can follow the same thing. Chat text is not uploaded.
- `topic save / list / show / check / rm`. A topic cannot be keyword-only. v1 cannot rename. Checking for new messages needs you to be signed in.
- People you already linked move to the account the first time this version talks to it. After that, searching by person also needs a connection.

# AnyChat 0.1.26

## User-visible changes

- First-time WeChat setup now diagnoses, then asks the human to install the supported app themselves. The host never installs or uninstalls WeChat and never touches chat databases.
- Recommended installers are hosted: macOS 4.1.5 and Windows 4.1.9. Already-readable archives are not force-downgraded. Prepare-access retries at most twice.

# AnyChat 0.1.25

## User-visible changes

- Product Signals now cover the full command funnel: search, export, media list/download, friends, groups, sessions, doctor, and completed setup. Fail-open, calling-thread, 500ms.

# AnyChat 0.1.24

## User-visible changes

- Authenticated searches now emit Product Signals (`search_results_returned`) by default. Emission is fail-open and runs on the calling thread so the CLI does not exit before the event is posted.
- Direct first-time WeChat setup on Windows stops at 4.1.10.30; 4.1.10.31+ fail closed instead of being sent into prepare-access.

## Package coverage

- macOS Apple Silicon: native binaries for `anychat` and `anychat-access`.
- Windows x64: binaries for `anychat.exe` and `anychat-access.exe`.
- Runtime manifests pin each artifact by SHA-256 and fail closed on unsupported platforms or checksum drift.
