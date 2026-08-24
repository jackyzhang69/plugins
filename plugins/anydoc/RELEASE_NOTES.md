# AnyDoc 0.2.1

- Reports completed assemble to Product Signals.
- Fail-open: a Signals outage does not block packing.

# AnyDoc 0.2.0

- Adds accountd-hosted typed private assembly models, exact model state handling, Teach Me after authoritative absence only, and plan/receipt v2 authority binding.
- Removes the old local preference-string knowledge command without migration or fallback.
- Ships a signed and Apple-notarized macOS Apple Silicon binary with pinned Pdfium. Windows is not included in this release.

# AnyDoc 0.1.1

## User-visible

- Canonical public package now lives at `~/.jackyzhang.app/plugins/anydoc/current`.
- `anydoc doctor --repair-install` copies this marketplace package into that tree, then deletes the previous tree if the OS allows it.
- Runtime stays in `~/.jackyzhang.app/anydoc/`. Shared login stays in `~/.jackyzhang.app/token/user.json`.

# AnyDoc 0.1.0

## User-visible

- Inspect a local folder of PDFs, photos, and Office files.
- Approve a fully expanded packing list, then assemble into a new subdirectory.
- Copy/rename, unencrypted PDF page ops, image-to-PDF, Office-to-PDF when LibreOffice is present, explicit compression, photo sheets.
- Optional Portal login for Tell Jacky and Guides. Explicit manual packing stays offline.

## Package coverage

- macOS Apple Silicon: `anydoc`
- Windows x64: `anydoc.exe`
- Runtime manifest pins each artifact by SHA-256 and fails closed on unsupported platforms or checksum drift.
- Not Apple-notarized. Not Windows-signed. Pdfium is not packaged.
