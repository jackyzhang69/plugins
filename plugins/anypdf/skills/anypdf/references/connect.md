# Connect AnyPDF

## Backend and transport (product contract)

The client uses `https://anypdf.jackyzhang.app` by default. Override with
`ANYPDF_BACKEND_URL` only for development or self-hosting. Product requests use
an in-memory short-lived exact-audience JWT; neither the durable Portal token
nor that JWT belongs in plugin files, prompts, reports, logs, or command
arguments. HTTP is accepted only for loopback development URLs; all other
backends must use HTTPS. Ordinary product verbs are JSON-first except `read`,
which prints Markdown.

## Resolve the client (agent only)

Do not call whichever `anypdf` is first on PATH.

Platform: macOS arm64 `darwin-arm64`; Windows x64 `win32-x64` (`anypdf.exe`).

This skill is an **Agent Skills** router. It may arrive via a host marketplace
plugin install, or because the human/agent attached the skill folder or the
full `plugins/anypdf/` tree (Cherry Studio, Cursor, and other Agent Skills
hosts included). Marketplace install commands are optional convenience.

**Preferred:** the package root is the directory that contains
`runtime-manifest.json` two parents above this file
(`…/plugins/anypdf/skills/anypdf/references/connect.md` → package root
`…/plugins/anypdf`).

1. When that package root exists (full tree), repair from **this** package's
   binary (the new tree), not from PATH and not from `current`:

   ```bash
   PACKAGE_BIN="<package-root>/bin/<platform>/anypdf"
   "$PACKAGE_BIN" doctor --repair-install
   ```

2. Then use only the live copy:

   ```bash
   ANYPDF="$HOME/.jackyzhang.app/plugins/anypdf/current/bin/<platform>/anypdf"
   ```

   Windows: `%USERPROFILE%\.jackyzhang.app\plugins\anypdf\current\bin\win32-x64\anypdf.exe`

**Skill-only attach:** if the host loaded only `skills/anypdf/` and there is no
package-root `bin/`, require that the canonical CLI path above already exists
and matches the skill's intended version (`$ANYPDF doctor --json`). Do not
invent a download URL or fall back to PATH. If the canonical CLI is missing,
ask the host to attach the full `plugins/anypdf/` tree from
`jackyzhang69/plugins` (or use a marketplace recipe that installs that tree),
then repair-install.

Every later agent command in this skill is `$ANYPDF …`. If `doctor` reports a
different version than the attached package, repair again from `$PACKAGE_BIN`
before filling.

## One shared user credential

- Every official product reuses `~/.jackyzhang.app/token/user.json` with `credential_kind=user` and `slot=user`.
- If that canonical slot already exists, run `$ANYPDF whoami --json`. Do not ask for another Portal token merely because AnyPDF is being used for the first time.
- AnyPDF exchanges the durable `jz_` credential for a short-lived `aud=anypdf` JWT held only in memory. The raw credential is never sent to the AnyPDF API.
- Retired product credentials such as `ap_live_`, `ap_admin_`, or `fb_` are not valid Portal credentials.

## Token delivery (host agent — mandatory)

The host agent performs connect **for** the human. Never tell them to open a terminal and run login themselves.

Accept input in this order:

1. **File containing the token (preferred).** Read the path in the agent tool channel. Pipe stdin only:

```bash
printf %s "$(cat -- "$TOKEN_FILE")" | "$ANYPDF" login --token-stdin --json
```

2. **Plaintext token in chat (allowed, discouraged).** Warn once that a file path is better. Feed stdin. Do not echo the token back.

3. **No token yet.** Ask for a file path or a paste. Still do not ask them to run terminal commands.

Hard rules:

- `--token <value>` / argv secrets are **forbidden**.
- Never put a real `jz_` in skill text, logs, screenshots, or JSON the human sees.
- After a successful connect, other official plugins must not re-prompt when `user.json` is present.
- A failed verification does not write `user.json`.

After connection, confirm with masked output from:

```bash
"$ANYPDF" whoami --json
```

## Error meaning

- `token_invalid` means stdin did not contain one complete `jz_` credential. Never echo the submitted value.
- `auth_failed` means accountd or AnyPDF refused the credential. Check that the existing Portal **user** slot is active; do not change the person's role to work around it.
- `identity_invalid` means the installed AnyPDF client and service disagree about the `whoami` response. Do not ask the human to create another token. Update AnyPDF and report the product defect.

## Talk to the human

Say only whether AnyPDF connected and what the person can do next. If connection is needed, ask for a token **file** (preferred) or a paste — never a terminal command. Do not expose token contents, internal HTTP, or raw JSON. A successful `whoami` reports only the user id, product role, scopes, form access, and expiry.

After a marketplace install, skill-folder attach, or package-tree update, run
`"$PACKAGE_BIN" doctor --repair-install` when the package root exists, then
`$ANYPDF doctor`. The live copy is `~/.jackyzhang.app/plugins/anypdf/current`.
If `doctor` reports a different version than the attached package, repair again
from `$PACKAGE_BIN` before filling.

## Recovery before escalation

Keep typed recovery details between tools. For a fill problem, follow the
bounded recovery procedure in [fill](fill.md) before suggesting
[tell-jacky](tell-jacky.md). Reconnect only after the client's one automatic authentication
retry has already failed; never send feedback automatically.
