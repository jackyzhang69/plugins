# AnyChat 0.1.23

## User-visible changes

- `anychat whoami` now prints the Portal account email when accountd includes it on the short-lived identity token.

## Package coverage

- macOS Apple Silicon: native binaries for `anychat` and `anychat-access`.
- Windows x64: binaries for `anychat.exe` and `anychat-access.exe`.
- Runtime manifests pin each artifact by SHA-256 and fail closed on unsupported platforms or checksum drift.
