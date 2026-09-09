# AnyChat 0.1.71

## User-visible changes

- On Windows, WeChat version detection reads executable metadata directly, fixing misdetections where WeChat 4.1.9 was mistaken for an unsupported build.
- On Windows, when WeChat is not running, AnyChat correctly advises opening and signing into WeChat instead of reporting that a pinned version is required.
- First-time local access for multi-shard Windows archives now scans until all database shards are unlocked rather than stopping after the first shard.
- Pinned notes and identity links require explicit confirmation (--yes / --confirm) before persisting to avoid unconfirmed draft saves. Topic saving directly creates the topic using the proposed name when --name is omitted.
