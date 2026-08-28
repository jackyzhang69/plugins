# anydoc (public plugin)

Published under `jackyzhang69/plugins` → `plugins/anydoc/`.

**Ships:** skills, install metadata, and prebuilt AnyDoc CLI packages for macOS
Apple Silicon and Windows x64 after both platforms pass native acceptance.

**Never ships:** customer documents, tokens, or private development materials. Pdfium ships only when `runtime-manifest.json` declares `pdfium.packaged=true`; the package verifier then requires the exact pinned library and checksum for each platform.

## Skills

One discovery file: `skills/anydoc/SKILL.md`. Playbooks live under
`skills/anydoc/references/` (connect, assemble, tell-jacky).

Inspect and an explicitly approved manual plan stay offline. Saved private models use the shared Portal login; backend failure never silently becomes a manual plan.

## Platforms

| Platform | Binary |
|----------|--------|
| macOS Apple Silicon | `bin/darwin-arm64/anydoc` |
| Windows x64 | `bin/win32-x64/anydoc.exe` |

Unsupported platforms fail closed. Checksums are required.

Signing and Pdfium delivery are stated in `runtime-manifest.json`, not inferred
from filenames. Official releases require macOS same-Team hardened-runtime
signing plus Apple notarization, and Azure Trusted Signing Authenticode
signatures with timestamps on the Windows executable and `pdfium.dll`. Both
platforms remain bound to one source commit, SHA-256 manifest, and native
acceptance.

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
