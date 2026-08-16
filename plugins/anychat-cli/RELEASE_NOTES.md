# AnyChat 0.1.20

## User-visible changes

- First-time WeChat/Weixin setup now follows one version-aware plan on macOS and Windows.
- When existing local access is already ready, setup continues directly regardless of the currently installed WeChat/Weixin version.
- WeChat/Weixin 4.1.0 through 4.1.10 can use direct first-time preparation.
- WeChat/Weixin 4.1.11 or newer stops before preparation and asks the customer to choose one of two explicit paths:
  - keep WeChat/Weixin at 4.1.10 or below; or
  - temporarily use 4.1.10 or below for one-time preparation, then upgrade again (recommended).
- Host agents receive exact steps for the selected path. AnyChat never installs, downgrades, or upgrades WeChat/Weixin silently.
- macOS permission failures are no longer mislabeled as Windows failures.
- Private admin feedback lists now default to unprocessed (`received`) items. Listing all statuses requires explicit `--all`.

## Package coverage

- macOS Apple Silicon: native binaries for `anychat` and `anychat-access`.
- Windows x64: binaries for `anychat.exe` and `anychat-access.exe`.
- Runtime manifests pin each artifact by SHA-256 and fail closed on unsupported platforms or checksum drift.
