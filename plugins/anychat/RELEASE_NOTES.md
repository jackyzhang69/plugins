# AnyChat 0.1.46

## User-visible changes

- History queries now read one consistent local archive state even while the chat app is writing new data.
- A changing source is retried only while it is actually moving; stable integrity failures stop safely without publishing an unusable cache.
- Tell Jacky diagnostics can distinguish a moving archive from a stable integrity failure without including chat content, identities, local paths, or access material.
