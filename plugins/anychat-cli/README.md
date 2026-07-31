# anychat-cli (staged public plugin)

Staged tree for eventual publish to `jackyzhang69/plugins`.

**Ships:** skills + runtime-manifest + (later) prebuilt binaries only.  
**Does not ship:** source, keys, decrypt notes, research trees.

## Skills

| Skill | Role |
|-------|------|
| `connect-anychat` | Portal token once |
| `anychat-capabilities` | Intent → CLI router (every session) |
| `anychat-setup` | First-run local archive access |
| `anychat-query` | Friend / group / search queries |
| `anychat-media` | Attachment list / download |
| `tell-jacky` | Feedback (confirm first) |

## Binary

Dev: install with `../scripts/install-local.sh` → `~/.local/bin/anychat`.  
Release: place under `bin/darwin-arm64/anychat` before marketplace publish.
