# AnyChat 0.1.90

Faster ordinary archive commands: readiness no longer re-verifies an already-completed chat-app conversion on every run, so resolve, groups, query, and search start much sooner on a warm machine.

Cache freshness still rejects changed main or WAL files quickly, and full content checks remain the authority when metadata looks unchanged.
