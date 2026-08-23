# anydoc (public plugin)

Published under `jackyzhang69/plugins` → `plugins/anydoc/`.

**Ships:** skills, install metadata, and a prebuilt AnyDoc CLI for macOS Apple Silicon.

**Never ships:** customer documents, tokens, or private development materials. Pdfium ships only when `runtime-manifest.json` declares `pdfium.packaged=true`; the package verifier then requires the exact pinned library and checksum for each platform.

## Skills

| Skill | Role |
|-------|------|
| `anydoc-assemble` | Inspect → plan → human approve → assemble → verify |
| `connect-anydoc` | Optional Portal token once (shared `user.json`) |
| `tell-jacky` | Feedback (confirm first) |

Inspect and an explicitly approved manual plan stay offline. Saved private models use the shared Portal login; backend failure never silently becomes a manual plan.

## Platforms

| Platform | Binary |
|----------|--------|
| macOS Apple Silicon | `bin/darwin-arm64/anydoc` |

Unsupported platforms fail closed. Checksums are required.

Signing and Pdfium delivery are stated in `runtime-manifest.json`, not inferred from filenames. AnyDoc 0.2.0 packages pinned Pdfium and must pass same-Team hardened-runtime plus Apple notarization gates. Windows is outside the 0.2.0 public package.

## Honesty

- AnyDoc inspects facts and executes an approved packing list.
- Model-bound packing first resolves one exact accountd-hosted private model; only authoritative absence starts Teach Me.
- It does not classify documents, OCR, fill official forms, or say a pack is ready to file.
- Encrypted / form / signed PDFs are copy-or-rename only.
- HEIC must be exported to JPEG or PNG first.

Connect (secure): `printf %s "$TOKEN" | anydoc login --token-stdin`.  
Tell Jacky: `anydoc feedback create … --user-confirmed`.

Stage the macOS package (repo-level): `scripts/stage-package`.
Verify staged package: `plugin/scripts/verify-package`.
