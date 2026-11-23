---
name: anyweb
description: >-
  READ THIS FIRST for AnyWeb. Fill a supported Express Entry profile in
  visible Chrome on this computer, stopping before submit. Take the user's
  information files first, compile one complete fact pack, then run. Do not
  quiz field by field. Save or forget a website
  login for reuse here. Connect or log in with the shared Portal token, check
  identity and doctor, and Tell Jacky with a feature request or bug
  report. Connect with Jacky, start a pair session, or use a join code
  so Jacky's assistant can look at this machine's AnyWeb status.
  This is not a general web automation tool.
when_to_use: |-
  Load on plugin start. Trigger phrases:
  "create or continue my Express Entry profile",
  "fill my EE profile up to the final review step",
  "fill from these applicant files", "here are the information files",
  "continue this supported website draft on my computer",
  "check or resume my AnyWeb task", "check my AnyWeb repair",
  "is the website repair ready", "connect or log in to AnyWeb",
  "save my Portal token for AnyWeb", "Tell Jacky about AnyWeb",
  "save my website login", "forget my website login",
  "answer this saved security question",
  "report an AnyWeb bug", "request an AnyWeb feature",
  "connect with Jacky", "pair session", "join code from Jacky".
---

# AnyWeb — host agent contract

Load this on plugin start and whenever the user asks about filling a
supported Express Entry profile, saved login, website repair, connect,
pair session, or Tell Jacky.

## Talk to the human

Use plain language. Report the state and what it means, not raw JSON or flags.
Never claim that AnyWeb can execute arbitrary website work in this release.
Never claim an application was submitted, complete, or ready to file.
For the supported Express Entry task, say clearly that AnyWeb stops before the
final Continue/submission boundary and never submits the profile. On the first
session after install, read [references/get-started.md](references/get-started.md)
before your first user-visible reply. After a version update, read
[references/whats-new.md](references/whats-new.md) and resume the prior intent
without asking the human to repeat it. Never ask the human to run doctor as
homework. Run the intended ordinary command first; when stdout is
`jz.plugin.envelope.v1`, follow its exact `status` and `continue_args` until
the product reports `ready`, then resume the sealed request. If `status` is
`blocked`, say the `say_to_user` sentence. Do not paste JSON into chat.

Ask for the applicant's information files first. Start the task to receive
its `needs`, then compile `local_inputs` using their `fact_ref` values and
start again so the product can preflight **before Chrome opens**. Prefer one
batch of known facts from the files; ask once for what the files cannot supply.
Before login, AnyWeb requires the facts needed by every observable branch.
For Express Entry, this includes eligibility answers even when continuing an existing draft.
If the result says the JSON does not match the schema (`obtain=host_self_correct`),
fix the JSON yourself from the files and resume — do not stop silent, and do not
ask the human to redo work the files already contain. If facts or the published
schema revision change, compile and preflight again; do not reuse an old ready
state. If `needs` are missing facts, ask once; do not look for a private site graph,
and do not start crawling or repairing the website from a fill grant.
Do not interview the human one website field at a time. Stop only for a fact the files cannot supply, a live email code, a
verification-app or CAPTCHA challenge, or an irreversible confirm. If an email
code does not take, keep the same Chrome session: the executor resends on that
page and you ask for the new code. Do not start another login. The human
does not type into the website; the executor logs in, answers saved security
questions, and fills.

## When the user asks "what can you do?"

Do not answer from memory of an older release. The command list below only says
how to run each command; run the live client and translate `commands --json`
into product language:

```bash
"$ANYWEB_BIN" commands --json
```

Lead with the supported Express Entry fill from the user's files. Repair-claim
checks and pair session are secondary. If the user is not connected, run
[connect](references/connect.md) first.

## Agent router — intents

| User intent | Host does | Human may be asked |
|---|---|---|
| "what can AnyWeb do" | Live `"$ANYWEB_BIN" commands --json`; translate to the supported Express Entry fill first | Connect once if not logged in ([connect](references/connect.md)) |
| continue this supported website draft / create or continue my Express Entry profile / fill from these applicant files / here are the information files / check or resume my AnyWeb task | [local tasks](references/tasks.md): schema → one fact pack → preflight → Chrome only after it passes | Information files first; missing facts or email codes to this assistant; website password preferably as a local file (chat paste is warned, not blocked); verification-app or CAPTCHA in the window; irreversible confirm only when the product stops for a person |
| continue my PR application on the PR Portal / fill IMM 0008, 5406, 5669, 5562 on the portal | [local tasks](references/tasks.md): `site="ircc-pr-portal"`, `task="fill-to-pre-submit"`; portal case number is a fact; portal login saved with `hosted-account set --site ircc-pr-portal` | The portal case number if the files do not have it; never creates a new application |
| apply for or renew a PR card on the PR Portal | [local tasks](references/tasks.md): `site="ircc-pr-portal"`, `task="prc-fill-to-pre-submit"`; uses the same saved portal login; `dashboard.action` is `"new_prc"` or `"view"` (with `prc.case_id`); urgent processing not supported | Client's given and family name; `prc.case_id` when continuing; explicit confirmation before creating a new application (`"new_prc"`) |
| apply for a Permanent Resident Travel Document on the PR Portal | [local tasks](references/tasks.md): `site="ircc-pr-portal"`, `task="prtd-fill-to-pre-submit"`; uses the same saved portal login; `dashboard.action` is `"new_prc"` or `"view"` (with `prc.case_id`); urgent processing not supported | Client's given and family name; `prc.case_id` when continuing; explicit confirmation before creating a new application (`"new_prc"`) |
| save my website login / forget my website login / answer this saved security question | [saved login](references/hosted-continuity.md) | Confirm before storing or replacing a saved login; security answers only when the product requests them |
| check my AnyWeb repair / is the website repair ready | [repair](references/repair.md): `repair claims --json`; translate queue status | Facts to reproduce a blocked repair when the product asks |
| connect or log in to AnyWeb / save my Portal token | [connect](references/connect.md): pipe Portal token via stdin | Token file path or one-time paste (never argv) |
| Tell Jacky about AnyWeb / report an AnyWeb bug / request an AnyWeb feature | [Tell Jacky](references/tell-jacky.md) | Confirm the exact draft before send |
| connect with Jacky / pair session / join code from Jacky | [pair-session](references/pair-session.md) | Confirm once that Jacky's assistant may look at this machine's AnyWeb fill, repair, or saved-login status |

Playbooks: [connect](references/connect.md), [saved login](references/hosted-continuity.md),
[local tasks](references/tasks.md), [repair claims](references/repair.md),
[Tell Jacky](references/tell-jacky.md), and
[pair-session](references/pair-session.md).

## Every command, one line each

Run each entry below as `"$ANYWEB_BIN" <command words and flags>`; the entries
show only the command words and their key flags. Every command prints JSON.
Ask the live client for every argument of every command: `"$ANYWEB_BIN" commands --json`.

- `doctor` checks this computer's AnyWeb setup; `doctor --repair-install` installs this package as the only copy.
- `login --token-stdin` saves the shared Portal token piped on stdin; add `--force` to replace one that is already saved.
- `logout` removes the saved Portal token from this computer.
- `whoami` shows which account this computer is signed in as, with the token masked.
- `repair claims` lists this account's website-repair requests with their queue status.
- `hosted-account set --site <site> --role <role> --credentials-stdin --hosted-custody-confirmed` saves a website login read from stdin.
- `hosted-account status --site <site> --role <role>` shows whether a login is saved for that site and role.
- `hosted-account forget --site <site> --role <role> --user-confirmed` deletes a saved login.
- `hosted-security-answer set --challenge-ref <ref> --answer-stdin` saves a security answer the website just accepted.
- `hosted-security-answer status --site <site> --role <role>` shows masked counts of saved security answers.
- `hosted-security-answer forget --site <site> --role <role> --user-confirmed` deletes saved security answers.
- `task start --request-stdin` starts a fill from the request on stdin and returns what is still needed, then runs the preflight and the fill.
- `task status --task-ref <ref>` shows the state of one task (hosts normally use the MCP step-status tool).
- `task resume --task-ref <ref> --input-stdin` gives a waiting task the facts it asked for; `--action request_passcode_resend --expected-passcode-epoch <n>` asks for the email code to be resent instead.
- `task cancel --task-ref <ref>` cancels a task and closes its runtime.
- `task result --task-ref <ref>` prints the final result of a finished task.
- `task forget-answers --input-stdin` deletes the answers an earlier version stored for this user and site.
- `runtime doctor` checks whether the signed local runtime is installed and verified.
- `runtime install` downloads and verifies the runtime declared by this package.
- `feedback create --type feature-request|bug-report --title <title> --description <text> --user-confirmed` sends Tell Jacky feedback the user approved.
- `feedback list` lists the feedback sent from this computer.
- `feedback status --id <id>` shows the status of one piece of feedback.
- `feedback inbox` shows unread replies and updates.
- `feedback read --update-id <id>` marks one reply or update as read.
- `pair join --code <code> --user-confirmed` joins a pair session with Jacky's assistant.
- `pair status` shows whether a connection is open.
- `pair snapshot --envelope-json <path>` sends the current product status.
- `pair inbox` shows unread instructions from Jacky's assistant.
- `pair read --message-id <id>` marks one instruction read after it was shown.
- `pair result --ok` sends the outcome of a requested action; add `--error <reason>` when it failed.
- `pair close` ends the connection.
- `mcp serve` runs the MCP stdio server for the host's step-status, resume and saved-login cache loop.
- `commands` prints this command surface as JSON.

## Live CLI discovery (fail-closed)

Ask the live CLI, never from memory of an older release:

```bash
"$ANYWEB_BIN" commands --json
```

The host loop for step status, resume, and saved-login cache is MCP:
`codex mcp add anyweb -- anyweb mcp serve`. Use `step_status`,
`task_resume`, and `credential_cache_*`. Do not shell `task status` /
`task resume` as a Codex hang-restart or CLI-as-fallback path. Tool
results never include passwords, passcodes, or security answers.

Resolve `$ANYWEB_BIN` from the current plugin package for this host. On macOS
Apple Silicon it is `bin/darwin-arm64/anyweb`; on Windows x64 it is
`bin/win32-x64/anyweb.exe`; on Linux x64 it is `bin/linux-x64/anyweb`. If the canonical current install is absent, run the
package binary's `doctor --repair-install --json`, then use
`~/.jackyzhang.app/plugins/anyweb/current/...`. Never choose a random PATH
binary over the package currently loaded by the host.

`runtime doctor` is read-only. If it reports `runtime_missing`, run the exact
`runtime install --json` continuation, then run `runtime doctor --json` again.
The install command downloads only the version-, platform-, size-, and
SHA-bound signed runtime declared by the current package; never substitute a
URL, path, or binary from chat.

Runtime data stays in `~/.jackyzhang.app/anyweb/`; the shared Portal identity
stays in `~/.jackyzhang.app/token/user.json`. Website work stays in visible
Chrome on this computer. A saved login is only for reuse on this computer and
never exposes the stored password or answer in chat. Never expose repair refs
as website content, and never treat `repair_available` as permission to submit,
sign, pay, upload, send, withdraw, delete, or make a final declaration.

## Updates

`anyweb` may print one stderr line with schema `jz.plugin.update_notice.v1`. It never blocks a command. Finish what the user asked first, then tell them once, in plain words, that a newer version is available and ask whether to update now. On yes: refresh this plugin through the host (Claude Code: `claude plugin marketplace update jacky-plugins`, then `claude plugin update anyweb@jacky-plugins`; Codex: `codex plugin marketplace upgrade jacky-plugins`, then `codex plugin add anyweb@jacky-plugins`; other hosts: attach the new `plugins/anyweb/` tree). Then run `doctor --repair-install` with the binary from the **new** package, not the folder this session started from (Claude Code: `~/.claude/plugins/cache/jacky-plugins/anyweb/<latest>/bin/<platform>/anyweb`; Codex: `~/.codex/plugins/cache/jacky-plugins/anyweb/<latest>/bin/<platform>/anyweb`; `<latest>` is the notice's `latest` value or the highest version folder present; `<platform>` is `darwin-arm64`, `linux-x64` or `win32-x64`). Read `references/whats-new.md` from that folder and tell the user in one or two sentences what changed and that the host loads it fully after a restart. For the rest of this session use that folder for every command and reference; the old version folder is gone. On no: do not ask again in this session.
