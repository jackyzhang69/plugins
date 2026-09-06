# AnyDoc 0.3.5

- Host agents can open a time-boxed pair session with Jacky's assistant using
  a join code. The CLI sends inspect/assemble status only; customer files,
  paths, and continuation tokens stay on this computer.

# AnyDoc 0.3.4

- Assembly now requires a named authority: a confirmed private model, or the
  user's own document list. A Public Guide can only supply conventions for
  deliverables the user already listed; it is never the authority. Folder and
  file names are not enough. Teach Me starts only when the model is truly
  absent.
- Client intake questionnaires stay out of every assembled output by default.
  One file is included only when the user names that file, and then AnyDoc
  says it is collection material, not a submission document.
- AnyDoc no longer invents a file-size cap. Planned page-size and orientation
  changes are shown before approval. If compression still exceeds a cited cap,
  the pack is delivered with a warning instead of failing the whole job.

# AnyDoc 0.3.3

- Uses one AnyDoc router skill with live `commands --json` discovery.
- Windows `anydoc.exe` and `pdfium.dll` are Authenticode-signed through an
  AnyDoc-only GitHub OIDC identity and Azure Trusted Signing. macOS remains
  hardened-runtime signed and Apple-notarized.
- Release hashes are recomputed after both platforms are signed, so the public
  manifest and sidecars describe the exact bytes users download.

# AnyDoc 0.3.2

- Resolves the canonical `~/.local/bin/anydoc` shim to the real installed
  executable before locating packaged Pdfium. `anydoc doctor --json` and PDF
  reading now work identically through PATH and through the package binary.

# AnyDoc 0.3.1

- Fixes the installed macOS runtime check for the signed Pdfium library. The
  release manifest binds the exact post-signing SHA-256, and Apple's hardened
  runtime remains responsible for same-Team dynamic-library validation.
- Release staging now runs the signed CLI and requires Pdfium to bind before a
  package can be published. Windows remains intentionally unsigned and keeps
  the upstream pinned hash, same-commit, x64 PE, and native acceptance gates.

# AnyDoc 0.3.0

- Assembly Model v2 represents both one combined multi-person file and one
  named file per person, with person-scoped conditions and claim ownership.
- Plan v3 separates model-pending work from present-but-unaccounted sources.
  Every current source is included, explicitly excluded, ignored system
  metadata, or pending; pending sources block approval.
- Approval receipts bind the current non-system input inventory. Added,
  removed, renamed, or same-path replaced sources invalidate execution.
- Native PDF and safe sandbox-converted Office text is read first. Host
  vision/OCR is reserved for no-text, protected, XFA-low-signal, or ambiguous
  pages; evidence basis is recorded locally.
- Working/intake documents and editable Office originals superseded by an
  included final PDF can be explicitly excluded without deleting originals.
- Official release requires both macOS arm64 and Windows x64. macOS remains
  signed and Apple-notarized; Windows is intentionally unsigned but must come
  from the same source commit, pass native Windows acceptance, and match the
  published SHA-256 manifest.

# AnyDoc 0.2.2

- Product Signals funnel: inspect, plan validate/approve, verify, render-page,
  extract-text, and doctor now emit after a real attempt. Assemble still emits
  on success and failure. Fail-open.

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
