# What's new in 0.1.85

**Windows provision** now surfaces the UAC elevation prompt when administrator rights are required, instead of exiting silently with code -1. Non-ASCII install paths are handled safely during elevation.

**Archive / shards:** SQLite lock-byte (reserved) pages are skipped in MAC verification, soft-fail shards are isolated, and reserved-page WAL is rejected before truncate—so fixed page failures such as page 262145 no longer wipe a usable archive.
