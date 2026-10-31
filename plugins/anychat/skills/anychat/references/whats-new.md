# AnyChat — what changed in 0.1.105

- Setup no longer reports message history as readable when only the archive keys have been verified. It checks that the message files can actually be opened and recognized, and keeps readable parts available when another part fails.
- Reading or searching messages keeps every message that can be read when part of the archive or a linked conversation cannot, including context reads, and marks the answer incomplete with a specific reason. Unsupported sibling tables no longer force setup again, and type filters ignore excluded unreadable bodies.
- This update corrects readiness reporting. It does not establish that the reported Windows key-recovery or message-reading failures are resolved.
