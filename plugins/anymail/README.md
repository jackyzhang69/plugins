# AnyMail package

Public package layout for the AnyMail agent-native mail CLI. The package
contains the single router skill, host metadata, and exactly two native
clients: `darwin-arm64` and `win32-x64`. It contains no OAuth client secrets,
mailbox passwords, or private development stores.

Load `skills/anymail/SKILL.md` first. Discover capabilities with the live CLI
(`commands`, `doctor`, `account list`), never from memory of an older build.

The packaged binary for the current platform lives under `bin/<platform>/`.
Prefer that binary (or `$ANYMAIL`) over whichever `anymail` happens to be first
on PATH.
