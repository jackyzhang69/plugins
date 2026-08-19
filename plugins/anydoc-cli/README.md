# anydoc-cli (public plugin)

Published under `jackyzhang69/plugins` → `plugins/anydoc-cli/`.

**Ships:** skills, install metadata, and prebuilt AnyDoc CLI binaries for macOS Apple Silicon and Windows x64.

**Does not ship:** customer documents, tokens, Pdfium libraries, or private development materials.

## Skills

| Skill | Role |
|-------|------|
| `anydoc-assemble` | Inspect → plan → human approve → assemble → verify |
| `connect-anydoc` | Optional Portal token once (shared `user.json`) |
| `tell-jacky` | Feedback (confirm first) |

Packing is offline. Do not block inspect/assemble on login.

## Platforms

| Platform | Binary |
|----------|--------|
| macOS Apple Silicon | `bin/darwin-arm64/anydoc` |
| Windows x64 | `bin/win32-x64/anydoc.exe` |

Unsupported platforms fail closed. Checksums are required.

This version is **not Apple-notarized** and **not Windows-signed**. macOS Gatekeeper may ask the human to allow the binary. Pdfium is not vendored; `doctor` reports `pdfium_unavailable` until a pinned library is present. Packing still works without it.

## Honesty

- AnyDoc inspects facts and executes an approved packing list.
- It does not classify documents, OCR, fill official forms, or say a pack is ready to file.
- Encrypted / form / signed PDFs are copy-or-rename only.
- HEIC must be exported to JPEG or PNG first.

Connect (secure): `printf %s "$TOKEN" | anydoc login --token-stdin`.  
Tell Jacky: `anydoc feedback create … --user-confirmed`.

Stage both platforms (repo-level): `scripts/stage-package`.  
Verify staged package: `plugin/scripts/verify-package`.
