# AnyChat 0.1.61

## User-visible changes

- Live support can deliver progress updates while the other assistant works, and keep receiving within your current assistant when its support hooks are enabled and trusted.
- Questions, progress, answers, and requests for your decision are distinguished. Progress does not mean the issue is resolved.
- Support respects pauses and cancellation. An open mailbox is no longer presented as proof that someone is actively receiving.
- Reply retries preserve the same message; a short receive now checks for unread messages before reporting a timeout.
