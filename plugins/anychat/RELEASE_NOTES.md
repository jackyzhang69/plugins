# AnyChat 0.1.85

- Windows provision: show the UAC elevation prompt instead of failing silent (exit -1) when admin rights are required; harden elevate path encoding for non-ASCII install paths.
- Archive: skip the SQLite reserved lock-byte page during MAC checks, isolate soft-fail shards, and reject reserved-page WAL before truncate so page-262145 style shard failures no longer dead-end usable archives.
