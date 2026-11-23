# AnyChat — what changed in 0.1.106

- AnyChat now tells you quietly, at most once a day, when a newer version is available, and it never interrupts what you asked for.
- Updating now leaves only the new version on this computer; older copies and leftovers are removed, and your login and chat data are kept.
- Search result reranking now uses the shared Jev judgement SDK, routed through OpenRouter.
- Problem reports from Linux now include the operating system version.
- Setup no longer reports message history as readable until the message files can actually be opened and recognized, and parts that can be read stay available when another part fails.
- Reading or searching messages keeps every message that can be read when part of the archive cannot, and marks the answer incomplete with a specific reason.
- This update corrects readiness reporting; it does not establish that the reported Windows key-recovery or message-reading failures are resolved.
