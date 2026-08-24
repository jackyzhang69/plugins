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
