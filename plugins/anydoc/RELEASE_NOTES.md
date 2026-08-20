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
- Optional Portal login for Tell Jacky / Guides / Remember Me. Packing stays offline.

## Package coverage

- macOS Apple Silicon: `anydoc`
- Windows x64: `anydoc.exe`
- Runtime manifest pins each artifact by SHA-256 and fails closed on unsupported platforms or checksum drift.
- Not Apple-notarized. Not Windows-signed. Pdfium is not packaged.
