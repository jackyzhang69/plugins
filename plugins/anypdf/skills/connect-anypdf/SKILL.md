---
name: connect-anypdf
description: Connect the public AnyPDF client once using the shared Portal user credential on stdin.
---

# Connect AnyPDF

## Resolve the client (agent only)

Do not call whichever `anypdf` is first on PATH.

Platform: macOS arm64 `darwin-arm64`; Windows x64 `windows-x64` (`anypdf.exe`).

This skill is loaded from a public plugin package. The package root is the directory that contains `runtime-manifest.json` two parents above this file (`skills/connect-anypdf/SKILL.md`).

1. After a marketplace install or update, repair from **this** package's binary (the new tree), not from PATH and not from `current`:

   ```bash
   PACKAGE_BIN="<package-root>/bin/<platform>/anypdf"
   "$PACKAGE_BIN" doctor --repair-install
   ```

2. Then use only the live copy:

   ```bash
   ANYPDF="$HOME/.jackyzhang.app/plugins/anypdf/current/bin/<platform>/anypdf"
   ```

   Windows: `%USERPROFILE%\.jackyzhang.app\plugins\anypdf\current\bin\windows-x64\anypdf.exe`

Every later agent command in this skill is `$ANYPDF …`. The human-typed one-time `anypdf login --json` is the PATH shim after repair; agents still use `$ANYPDF`. If `doctor` reports a different version than the marketplace plugin, repair again from `$PACKAGE_BIN` before filling.

## One shared user credential

- Every official product reuses `~/.jackyzhang.app/token/user.json` with `credential_kind=user` and `slot=user`.
- If that canonical slot already exists, run `$ANYPDF whoami --json`. Do not ask for another Portal token merely because AnyPDF is being used for the first time.
- AnyPDF exchanges the durable `jz_` credential for a short-lived `aud=anypdf` JWT held only in memory. The raw credential is never sent to the AnyPDF API.
- Retired product credentials such as `ap_live_`, `ap_admin_`, or `fb_` are not valid Portal credentials.

## One-time local connection

The human runs the following command in their own terminal and enters the Portal credential at the non-echoing prompt:

```bash
anypdf login --json
```

Do not ask the human to paste the credential in chat, attach it, expose a local token file, or let an agent read or relay it. The CLI accepts the credential only through its bounded non-echoing stdin prompt and has no token argument. The secret is never placed in argv, shell history, stdout, stderr, a screenshot, or a report. A failed verification does not write `user.json`.

After connection, the agent may confirm with masked output from:

```bash
$ANYPDF whoami --json
```

## Error meaning

- `token_invalid` means stdin did not contain one complete `jz_` credential. Never echo the submitted value.
- `auth_failed` means accountd or AnyPDF refused the credential. Check that the existing Portal **user** slot is active; do not change the person's role to work around it.
- `identity_invalid` means the installed AnyPDF client and service disagree about the `whoami` response. Do not ask the human to create another token. Update AnyPDF and report the product defect.

## Talk to the human

Say only whether AnyPDF connected and what the person can do next. If connection is needed, give the single local command above without asking for the credential itself. Do not expose token contents, internal HTTP, local paths, or raw JSON. A successful `whoami` reports only the user id, product role, scopes, form access, and expiry.

After a marketplace install or update, run `"$PACKAGE_BIN" doctor --repair-install`, then `$ANYPDF doctor`. The live copy is `~/.jackyzhang.app/plugins/anypdf/current`. If `doctor` reports a different version than the marketplace plugin, repair again from `$PACKAGE_BIN` before filling.
