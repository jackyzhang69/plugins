# AnyChat

Search, export, and inspect chat history already stored on your own computer.
Chat content stays on that computer.

## Supported computers

| Platform | Local archive support |
|----------|---------------------|
| macOS Apple Silicon | Verified local social-app archives on this computer |
| Windows x64 | Verified local social-app archives on this computer |

## Supported AI agent hosts

| Host | Install |
|------|---------|
| Claude Code | Marketplace `jackyzhang69/plugins`, plugin `anychat` |
| Codex | Marketplace `jackyzhang69/plugins`, plugin `anychat` |
| DeepSeek Harness (`dsh`) | `dsh plugin --profile web add "github:jackyzhang69/plugins#anychat-vX.Y.Z&path:plugins/anychat"` (requires `pnpm` on PATH; restart `dsh web` afterwards) |

The same skill contract and the same signed binaries ship to every host.

Unknown chat-app builds fail closed. Missing, older, newer, or wrong-build clients get only the exact AnyChat-provided package for their platform; after the human agrees, the host agent verifies and installs it, and AnyChat independently validates the result. The human never finds paths, manages applications, runs commands, or installs software.

Your AI agent handles setup and computer work. You only approve a material
change, enter an operating-system password, choose an account, or sign in when
those actions cannot be delegated.

AnyChat validates the app version, archive readiness, permissions, and requested
result before reporting success. Safe operating-system and app-version facts may
be included in a user-approved problem report; chat content and credentials are
not included.

See [`RELEASE_NOTES.md`](./RELEASE_NOTES.md) for the current release changes.
