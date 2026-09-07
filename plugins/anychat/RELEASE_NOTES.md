# AnyChat 0.1.66

## User-visible changes

- First-time setup on Windows now also looks for the chat app’s own database configuration objects in memory, not only leftover key text. This can unlock a signed-in archive that previously finished scanning with no key.
- If that still fails, setup stays blocked and can send diagnostics to Jacky. It will not ask you to open extra chat windows or quit the app.
