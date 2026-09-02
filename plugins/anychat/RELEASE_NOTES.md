# AnyChat 0.1.47

## User-visible changes

- AnyChat now runs inside DeepSeek Harness (`dsh`). The same package installs as a dsh bundle: `dsh plugin --profile web add "github:jackyzhang69/plugins#anychat-v0.1.47&path:plugins/anychat"`, then restart `dsh web`.
- Every agent host (Claude Code, Codex, DeepSeek Harness) reads the same skill contract and runs the same signed binaries; chat content still never leaves your computer.
