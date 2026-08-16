# AnyChat 0.1.21

## User-visible changes

- When multiple WeChat/Weixin builds coexist, `doctor --json` now returns an actionable `setup_plan.version_confirmation_choices` entry for every detected build.
- After the user chooses, the host agent records the exact detected build with `anychat setup --confirm-wechat-build <exact-build>`.
- `doctor`, `prepare-access`, and `sources connect wechat` all reuse that recorded confirmation while the exact build remains live-detected.
- Invented or stale build values fail closed with `E_WECHAT_VERSION_CONFIRMATION_INVALID`; they never bypass live detection.
- Existing access material still continues directly, and the 4.1.0–4.1.10 versus newer-version routing remains unchanged.

## Package coverage

- macOS Apple Silicon: native binaries for `anychat` and `anychat-access`.
- Windows x64: binaries for `anychat.exe` and `anychat-access.exe`.
- Runtime manifests pin each artifact by SHA-256 and fail closed on unsupported platforms or checksum drift.
