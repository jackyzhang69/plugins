# AnyChat 0.1.41

## User-visible changes

- On both macOS and Windows, first-time archive access now uses only the exact chat-app package supplied by AnyChat. Your AI agent asks before replacing the app, verifies the package, performs the work, and leaves chat history alone.
- Your AI agent owns computer work such as finding the app or archive, opening and closing apps, and installing the required package. You only approve material changes, enter an operating-system password, choose an account, or sign in when necessary.
- Setup no longer ends because of a retry count. It continues when the computer state changes and resumes the original request after the archive is ready.
- Local plain, classic-XOR, and V1 images can now be converted to normal files; when the preferred copy is unusable, AnyChat also checks its contained lower-quality copy. Unknown and V2 containers still return an actionable unsupported result instead of pretending the image was recovered.
