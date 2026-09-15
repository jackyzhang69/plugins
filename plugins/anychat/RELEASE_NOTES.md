# AnyChat 0.1.86

- Setup / query: stop treating historical WeChat setup_complete as product-wide permission; live per-source readiness gates execution. Setup repair reconciles dirty 0.1.85-style host state from archive evidence (do not hand-edit config.json).
- WeChat matrix / coverage: honest Unknown vs unmatched vs matched Partial wording, selected-folder vs history-source completeness, and bounded global scan-batch continuation (incl. zero-hit) so Partial/coverage reporting matches what was actually scanned.
- Continues 0.1.85: Windows UAC elevation prompt (no silent -1) and SQLite lock-byte / soft-fail shard isolation.
