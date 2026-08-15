# AnyChat v0.2 — Release Candidate Notes

**Status:** unreleased draft. This file does not authorize a version bump, tag, asset upload, deployment, or production change.

## Pitch

AnyChat v0.2 turns three verified local archives into one agent-facing search surface: WeChat, iMessage, and Telegram can be queried together without uploading chat content or flattening source identity. A new evidence command consumes every result page and creates a private bundle whose hashes, file set, provenance, coverage, and attachment claims can be checked offline.

## Supported-source matrix

| Source | macOS ARM64 | Windows x64 | Coverage and hard gates |
|---|---|---|---|
| WeChat / Weixin | Verified profiles only | Verified profiles only | Unknown builds fail closed. Automatic first-time archive acquisition remains a separate per-build gate. |
| iMessage | macOS 14.x verified profile | Not applicable | Local `chat.db` snapshot only; account selection is explicit; exact contained attachments only. |
| Telegram | `12.9 (282526)` Postbox profile | Not available | Local cached history only. Local passcode is `ActionRequired`; Windows `tdata` needs a native Spike. |
| WhatsApp | Not available (macOS 1/3) | Not available (Windows 0/3) | Feasibility evidence is not a Connector. Offline attribution and platform-independent Go Gates remain blocking. |
| Signal | Not available (0/3) | Not available | Official Desktop Backup is a conditional technical path; real evidence cells and written clean-room/legal approval are required. |

## User-visible changes

- Source Registry, explicit accounts, source-qualified provenance, typed partial failures, and fan-out across stable sources.
- Continuation v2 pins the original scope and resumes only sources that issued a cursor; ambiguous v1 tokens are rejected.
- `anychat evidence create` writes a private, no-clobber bundle; `anychat evidence verify` checks hashes and cross-file semantics.
- Exact iMessage attachment reads and hardened legacy WeChat media containment.
- Entitlement refreshes are serialized; the durable Portal token is exchange-only and product requests use a content-free metadata allowlist.
- Local developer installs preserve the previous main/helper pair. Backups are never automatically deleted:
  - `scripts/install-local.sh --list-backups`
  - `scripts/install-local.sh --rollback <backup-id>`
  - `scripts/install-local.sh --purge-backup <backup-id>`

## Coverage truth

- Cross-source identity is never merged from display names.
- There is no materialized global message index; queries fan out to native connectors.
- Evidence includes attachment bytes only when the Connector proves an exact source-native association. Unsupported or heuristic media is recorded as an exclusion.
- A valid cached entitlement allows local/offline content operations during its validity window. Expired entitlement continues to fail closed.
- `Tell Jacky` remains the only user-approved feedback path; diagnostic context rejects chat content, contacts, paths, and unknown fields.

## Release gates still open

Before any v0.2 release:

1. Jacky explicitly authorizes the release and chooses the version/tag.
2. Licensing metadata is resolved: the Codex descriptor currently declares MIT while this repository has no committed license file.
3. Fresh macOS install, query, media, evidence, network-blocked, upgrade, downgrade, rollback, and per-source live-smoke receipts pass on the release candidate.
4. Final Windows-native runner/real-machine validation passes; cross-compilation is not native evidence.
5. `scripts/stage-package` rebuilds the exact candidate; `plugin/scripts/verify-package`, full tests, leak gate, Atlas gate, and GitHub tag/asset/hash checks pass.
6. Release assets and tag are created only after the preceding gates; no existing asset is reused by version string alone.
