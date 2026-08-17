# AnyChat 0.1.22

## User-visible changes

- First-time setup is now an agent-owned protocol. `doctor --json` returns `setup_plan.agent` with what to say to the human, the one OS click they may need, and the exact argv the host agent runs.
- The human never uses a terminal. Host agents must not show commands or ask for WeChat version numbers.
- When only one WeChat/Weixin is running, AnyChat selects that build automatically.
- If two WeChat apps are open, the agent asks the human to leave only the one they chat with. Closing a window is not enough; they must Quit from the menu bar or tray.
- Newer WeChat versions no longer push a downgrade as the default. The recommended path is: keep the current WeChat, and the agent reports the gap.
- A macOS process-access denial is no longer reported as “method unavailable” just because another installed WeChat is unsupported.
- Each prepare-access run writes a fresh diagnosis. Stale fields from an older AnyChat cannot survive.

## Package coverage

- macOS Apple Silicon: native binaries for `anychat` and `anychat-access`.
- Windows x64: binaries for `anychat.exe` and `anychat-access.exe`.
- Runtime manifests pin each artifact by SHA-256 and fail closed on unsupported platforms or checksum drift.
