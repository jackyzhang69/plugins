# AnyChat 0.1.105

- Readiness now reports message history as unavailable when its files cannot actually be opened, even if their keys verify. Readable message parts remain available when another part fails.
- Message history can refresh while another query is still reading an older view, including on Windows.
- Reading or searching chat messages no longer fails outright when one row, table or archive part cannot be read. Readable messages are returned, the result is marked incomplete, and the failure reports a specific reason (unsupported schema, unreadable row or body, locked or corrupt database) instead of a generic query failure.
- Context reads retain incomplete-history warnings, and linked conversations keep readable messages when another conversation fails. Unsupported sibling tables no longer force setup again, and type-filtered queries do not decode bodies they exclude.
- Problem reports now show, for each message part, whether its key was accepted, why it did not open (for example a page that failed its integrity check, and where that page sits in the file), and whether its layout was recognized. They carry stage labels and counts only, never paths or content.
- The original Windows key-recovery and message-reading reports remain under investigation; this update does not claim their recovery.
