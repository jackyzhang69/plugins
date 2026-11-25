---
name: formbro
description: >-
  READ THIS FIRST for FormBro. Fill the webform for X, fill IMM5257 / IMM0008 /
  IMM5645 / IMM5709, find X case, list applications, PDF for case X, is this
  ready to submit, check formbro status, connect / log in / save my jz_ token,
  tell Jacky. IRCC IMM PDFs and local portal fills (never submits). One
  discovery file; playbooks in references/. Ask the live CLI
  (`formbro commands --json`), never from memory of an older release.
when_to_use: |-
  Load on plugin start. Trigger phrases: "fill the webform for X", "find X case",
  "list applications", "PDF for case X", "is this ready to submit",
  "check formbro status", "my token / which backend",
  "connect to formbro / set up formbro", "log in to formbro / save my jz_ token",
  "fill IMM0008 / IMM5257 / IMM5710 / IMM5406 / etc.",
  "give me the filled PDFs for case X", "generate the IMM forms",
  "fill the webform for <person>", "open the IRCC portal and fill",
  "automate the LMIA Job Bank application", "preflight this case for webform fill",
  "can my machine run a fill / is the worker daemon up",
  "find/search/look up a person", "what's the status of a case",
  "create / update / patch / set the X field on Y", "tell Jacky about this",
  "report this bug / file a bug report".
  Bare "@formbro" / "what can formbro do": connect if not logged in, else this contract.
---

# FormBro plugin — agent consumption contract

## Talk to the human (mandatory)

Speak in everyday words. Say what is happening and what is next. Do not lead
with binary paths, `--help`, or raw JSON. FormBro prepares; the user submits.
Never show tokens or immigration case field values in chat unless the user
asked for that detail.

## Live CLI surface (fail-closed)

Ask the live CLI, never from memory of an older release. Volatile catalogs
(form lists, live case data) are not dumped.

```bash
"$FORMBRO_BIN" commands --json
```

Playbooks: [connect](references/connect.md), [read](references/read.md),
[write](references/write.md), [fill](references/fill.md),
[webform](references/webform.md), [tell-jacky](references/tell-jacky.md).

`formbro commands --json` prints every public command with its arguments as JSON; use it whenever you need the exact current list. `formbro health` takes no arguments and returns the backend's health status as JSON.

## Updates

`formbro` may print one stderr line with schema `jz.plugin.update_notice.v1`. It never blocks a command. Finish what the user asked first, then tell them once, in plain words, that a newer version is available and ask whether to update now. On yes: refresh this plugin through the host (Claude Code: `claude plugin marketplace update jacky-plugins`, then `claude plugin update formbro@jacky-plugins`; Codex: `codex plugin marketplace upgrade jacky-plugins`, then `codex plugin add formbro@jacky-plugins`; other hosts: attach the new `plugins/formbro/` tree). Then run `doctor --repair-install` with the binary from the **new** package, not the folder this session started from (Claude Code: `~/.claude/plugins/cache/jacky-plugins/formbro/<latest>/bin/<platform>/formbro`; Codex: `~/.codex/plugins/cache/jacky-plugins/formbro/<latest>/bin/<platform>/formbro`; `<latest>` is the notice's `latest` value or the highest version folder present; `<platform>` is `darwin-arm64`, `linux-x64` or `win32-x64`). Read `references/whats-new.md` from that folder and tell the user in one or two sentences what changed and that the host loads it fully after a restart. For the rest of this session use that folder for every command and reference; the old version folder is gone. On no: do not ask again in this session.

## Shared platform token (host agent — mandatory)

- Canonical durable user credential: `~/.jackyzhang.app/token/user.json` (`jz_` only).
- **One connect for the whole platform.** If this file already exists from any other official plugin, do **not** ask the human to log in again.
- On load, if `user.json` is missing but a legacy on-disk `jz_` exists (`token/user.json`, product-local config token fields), **migrate immediately** by running this product's CLI `whoami`/`doctor`/any authenticated command (load-time migrate is built in). User must feel no re-paste.
- Only when no migratable `jz_` exists: complete connect once using the token-delivery rules below. After that, every registered plugin reuses the same slot.
- Never print or log the raw token. Prefer masked doctor/whoami output.
- Do not create product-local durable token files.

## Token delivery to the host agent (connect) — LOCKED 2026-08-14

The host agent performs connect **for** the human. **Never** tell the human to open a terminal and run login commands themselves.

Accept input in this order:

1. **File containing the token (preferred).** If the human provides a filesystem path (e.g. `~/Desktop/jacky-token.txt`) or an attached/readable file whose contents are a single `jz_…` value (optional surrounding whitespace/newline only):
   - Read the file in the agent tool channel.
   - Pipe the token to the product CLI via stdin only: `login --token-stdin` (or the product's equivalent).
   - Do **not** put the token on argv, in chat echo, in logs, or in screenshots.
   - Confirm success with masked doctor/whoami only.

2. **Plaintext token in chat (allowed, discouraged).** If the human pastes a raw `jz_…` into the conversation:
   - **Warn once** in plain language: prefer a local file path next time so the secret is not retained in chat history; do **not** refuse.
   - Proceed immediately: feed that value to `login --token-stdin` via a non-echoing agent-side pipe (temp file mode 0600 deleted after use is OK; never `echo TOKEN |` in a way that lands in shell history if avoidable).
   - Do not repeat the raw token back in the reply.

3. **No token yet.** Ask the human to send either a **file path** (best) or paste the token. Still do not ask them to run terminal commands.

Hard rules:

- `--token <value>` / argv secrets remain **forbidden** for the CLI.
- Agent may read a user-supplied path and stdin-feed the CLI; that is the supported file path.
- After any successful connect, other plugins must not re-prompt when `user.json` is present.


# FormBro plugin — agent consumption contract

**Read this once on plugin load and reload it whenever a user asks anything FormBro-related.** It tells you which skill / subcommand to call for each user intent, and what to never guess.

## 0. Non-negotiable operating rules

1. **All FormBro system operations go through the bundled CLI.** Reads, creates, patches, attaches, deletes, validation, PDF generation, Excel export, uploads, notes, audit, and webform automation must use `<formbro> ...`. Do not call FormBro backend endpoints directly and do not write to MongoDB / any database directly.
2. **Local file parsing is allowed only before the CLI boundary.** The agent may read Excel, CSV, Word/DOCX, PDF, image, email, or text files locally to extract facts. The moment data is validated, saved, patched, exported, or filled, call the CLI.
3. **For new cases from external files, use `import`, not ad hoc cloning.** Ask FormBro for the contract/schema with `formbro import contract`, generate JSON locally, run `import apply-json --dry-run`, then rerun without `--dry-run` only when valid.
4. **For existing entities, use `extract` / `validate` / `patch`.** Do not use the new-case import path to mutate an existing applicant/application/employer.
5. **Never guess program keys, entity types, or command flags.** Use `programs list`, `programs describe`, `programs schema`, and `--help`. Current flags use `--program-key`, not legacy `--program`.
6. **`validate` passing alone is NOT "ready to submit" for automated webform filling.** `validate by-id` / `validate person` checks the SAVED DATA MODEL only (schema + business rules) — it never touches the government-portal adapter. `webform preflight` is the only check that dry-runs that adapter/transform layer against real data. Before telling a user or agent a case is fill-ready, both must return clean. `webform start` without `--confirmed` is NOT a readiness check: it returns a static dry-run envelope with no backend call; with `--confirmed` it runs compute-actions (backend cascade preflight) and then opens the browser. Never report "ready to submit" / "fill-ready" off a single one of these checks.

## Agent quick router — TOP 20 LINES (read this first)

User said this → call this exact command (binary resolution: §B; full router with TR/PR/LMIA detail: §1):

| User intent | Command (one-hop preferred) |
|---|---|
| "create/import a case from Excel/Word/PDF/files" | `formbro import contract --program-key <key>` -> local agent parses files to JSON -> `formbro import apply-json --program-key <key> --json '<json>' --dry-run` -> rerun without `--dry-run` |
| "patch this existing case/person from a file" | `formbro extract contract --program-key <key> --entity-type <T>` -> local agent parses file to JSON -> validate -> `formbro extract apply-json ...` or `persons/applications/employers patch` |
| "fill the webform for `<person>`" | `formbro webform start --query "<person>" --program-key <key> --confirmed` (v1.5.1+ unified; --query OR --app-id) |
| "update / overwrite an existing PR portal case by Case ID" | `formbro webform start --app-id <id> --program-key <key> --case-id <6 or 7 digits> --form 0008\|5406\|5669\|5562 --confirmed` (one form per run; see references/webform.md) |
| "invite Job Bank candidates for posting `<id>`" | `formbro webform jobbank-invite --job-post-id <id> --rcic-id <rcic_id> --minimum-stars 2\|4 [--max-invites 3] --confirmed` |
| "fill PDF / IMM forms for `<person>`'s case" | `formbro fill --app-id <id> --forms IMM…,IMM…` (see references/fill.md) |
| "find / look up `<person>`" | `formbro find "<person>" --include applications --limit 10` |
| "list ALL cases / inventory / audit sweep" | `formbro applications inventory [--program-key <key>] [--limit <n>] [--query <text>]` (returns every status incl. empty; default limit 200, default query matches everyone) |
| "list my active workbench" | `formbro applications list [--program-key <key>]` (dashboard semantics: only active statuses) |
| "is this case ready to submit" (for webform fill) | BOTH `formbro validate by-id --entity-type <T> --entity-id <id>` AND `formbro webform preflight --app-id <id> --program-key <key>` must be clean (Rule 6). `webform start` without `--confirmed` is only a static dry-run envelope (no backend call, no validation) — do not use it as a readiness check |
| "is my plugin healthy / which backend am I on / token still valid" | `formbro doctor --json` (live whoami + backend round-trip) |
| "can my machine even run a fill" | `formbro webform runtime-check` |
| "the daemon is acting weird" | `formbro webform daemon status` → `daemon restart` |
| "list my RCIC profiles / what RCIC is linked to this case?" | `formbro rcic list` returns each profile with its saved portal credentials; `formbro applications get <application-id>` shows the case's linked `case.rcic_id`. |
| "get the portal login (PR / TR / REP / LMIA) for an RCIC" | `formbro rcic list` → the profile's `credentials.<portal>.username` / `.password` (and security Q&As). Give them to the user or the filling tool that logs in; do not echo them into logs, notes or feedback. Edit them in FormBro → Settings → RCIC. |
| "plugin out of date?" | `formbro doctor --check-upgrade` |
| "update the plugin / install it cleanly / remove old copies" | Follow the Updates section: refresh through the host, then `formbro doctor --repair-install` from the new package. Login and settings are kept; the old tree is deleted. |
| "tell Jacky about this bug/feature/tip" | `formbro feedback create --type bug-report\|feature-request\|knowledge-tip --title "<t>" --description "<d>" [--application-id "<exact application id>"]` — `--application-id` is mandatory for bug reports; **always confirm the draft with the user first** (see [tell-jacky](references/tell-jacky.md)) |

Two semantic distinctions to NEVER conflate:
- `applications list` = consultant's active workbench (dashboard scope; filters by active status)
- `applications inventory` = every application in the account regardless of state (audit / batch / debugging)

`case.rcic_id` links an application to an **account-level RCIC profile**, not to an application person. To link a profile, use the `id` from `formbro rcic list`, then `formbro applications patch <application-id> --set case.rcic_id=<rcic-id>`. Create or edit the profile in FormBro → Settings → RCIC, not with `persons create`. Portal credentials are returned only to the profile's owner account.

Routing detail below is supplementary — start with this 20-line table.

## §B. Resolving the `formbro` binary

The plugin ships a Rust CLI binary that is NOT placed on `PATH` automatically by either Codex or Claude Code. Throughout these skills, `<formbro>` or the literal token `formbro` mean **"the bundled binary at this resolved path"**, not a `PATH` lookup.

**Resolution order (use the first that resolves to an existing executable):**

1. **`$FORMBRO_BIN`** — explicit override. Honor if set.
2. **Codex plugin cache** — `$HOME/.codex/plugins/cache/jacky-plugins/formbro/<version>/bin/<platform>/formbro` where `<version>` is the highest version dir present and `<platform>` matches a bundled OS/arch. Current public marketplace bundles ship `darwin-arm64`, `win32-x64` and `linux-x64`.
3. **Claude Code plugin dir** — `$CLAUDE_PLUGIN_ROOT/bin/<platform>/formbro` (Claude Code sets `CLAUDE_PLUGIN_ROOT` when invoking a plugin's skill).
4. **`which formbro`** — if the user has installed it on PATH manually.

**Portable resolver (bash; works on darwin / linux; for Windows agents use PowerShell variant below):**

```bash
# Explicit override wins before platform detection; useful for dev installs on
# platforms where the public package does not ship a bundled binary.
_formbro_override="${FORMBRO_BIN:-${FORMBRO_BIN_OVERRIDE:-}}"
if [ -n "$_formbro_override" ] && [ -x "$_formbro_override" ]; then
  FORMBRO_BIN="$_formbro_override"
  export FORMBRO_BIN
  "$FORMBRO_BIN" --help >/dev/null || { echo "FormBro CLI at $FORMBRO_BIN is not runnable" >&2; return 1 2>/dev/null || exit 1; }
  return 0 2>/dev/null || exit 0
fi

# Detect bundled platform → cache subdir name used by both codex and claude.
# Current public POSIX bundles include darwin-arm64 and linux-x64; Windows agents
# use the PowerShell resolver below for win32-x64.
case "$(uname -s)-$(uname -m)" in
  Darwin-arm64)  PLAT=darwin-arm64 ;;
  Linux-x86_64)  PLAT=linux-x64 ;;
  *) PLAT="" ;;
esac

# Walk a search list in priority order; pick first existing executable.
FORMBRO_BIN=""
_cand_paths=(
  "${CLAUDE_PLUGIN_ROOT:+${PLAT:+$CLAUDE_PLUGIN_ROOT/bin/$PLAT/formbro}}"
)
# Codex cache may have several version dirs; agent picks the *highest* one.
# We use python sort (universally available) — POSIX `sort -V` is not portable.
_codex_root="$HOME/.codex/plugins/cache/jacky-plugins/formbro"
if [ -n "$PLAT" ] && [ -d "$_codex_root" ]; then
  _latest_codex=$(python3 - "$_codex_root" <<'PY' 2>/dev/null
import os, sys
root = sys.argv[1]
def keyfn(d):
    try:    return tuple(int(x) for x in d.split('.'))
    except: return (-1,)
dirs = [d for d in os.listdir(root) if os.path.isdir(os.path.join(root, d))]
dirs.sort(key=keyfn)
print(os.path.join(root, dirs[-1]) if dirs else "", end="")
PY
)
  [ -n "$_latest_codex" ] && _cand_paths+=("$_latest_codex/bin/$PLAT/formbro")
fi
_cand_paths+=("$(command -v formbro 2>/dev/null)")

for _p in "${_cand_paths[@]}"; do
  if [ -n "$_p" ] && [ -x "$_p" ]; then FORMBRO_BIN="$_p"; break; fi
done

if [ -z "$FORMBRO_BIN" ]; then
  echo "FormBro CLI not found on this host. Current public bundles support darwin-arm64, linux-x64 and win32-x64 (via PowerShell). Install a platform-specific CLI and set FORMBRO_BIN if needed." >&2
  return 1 2>/dev/null || exit 1
fi
export FORMBRO_BIN
"$FORMBRO_BIN" --help >/dev/null || { echo "FormBro CLI at $FORMBRO_BIN is not runnable" >&2; return 1 2>/dev/null || exit 1; }
```

**Windows PowerShell variant** (codex on Windows installs to `$env:USERPROFILE\.codex\...`):

```powershell
$plat = "win32-x64"
$cands = @($env:FORMBRO_BIN_OVERRIDE)
if ($env:CLAUDE_PLUGIN_ROOT) { $cands += "$env:CLAUDE_PLUGIN_ROOT\bin\$plat\formbro.exe" }
$codexRoot = "$env:USERPROFILE\.codex\plugins\cache\jacky-plugins\formbro"
if (Test-Path $codexRoot) {
  $latest = Get-ChildItem $codexRoot -Directory | Where-Object { $_.Name -match '^\d+(\.\d+){1,3}$' } | Sort-Object { [version]$_.Name } | Select-Object -Last 1
  if ($latest) { $cands += "$($latest.FullName)\bin\$plat\formbro.exe" }
}
$cands += (Get-Command formbro -ErrorAction SilentlyContinue).Source
$env:FORMBRO_BIN = $cands | Where-Object { $_ -and (Test-Path $_) } | Select-Object -First 1
```

Once `$FORMBRO_BIN` is set, **every command in this doc and every other formbro skill** that starts with the bare token `formbro` should be invoked as `"$FORMBRO_BIN"` (bash) / `& $env:FORMBRO_BIN` (PowerShell). The bare `formbro` token is shorthand throughout these docs; the resolution rule applies uniformly.

**Trust-boundary note**: the `command -v formbro` fallback trusts the ambient PATH. Prefer the explicit cache paths above when both are available; PATH lookup is the last resort, not the canonical answer.

**Why this matters**: previously the docs wrote `formbro <subcommand>` assuming PATH was set, which silently fails in Codex (binary lives in cache, not PATH). Don't waste tool calls hunting for it — resolve once at session start.

## 1. Skill router by user intent

| User says (any phrasing) | Call | Skill |
|---|---|---|
| "find / search / look up <person or company>" | `formbro find "<query>"` | references/read.md |
| "what's the status of <case/applicant>" | `formbro applications get <id>` (TR/PR/LMIA all) **OR** `formbro applications status <id>` | references/read.md |
| "list cases for <client>" or "all <program> applications" | `formbro applications list --program-key <key>` | references/read.md |
| "show me employer <name>" | `formbro employers list --search` then `employers get` | references/read.md |
| "validate this application / can I submit?" | `formbro validate by-id --entity-type <T> --entity-id <id>` — data-model check only; if the case will go through automated webform filling this is NOT sufficient on its own, also run `webform preflight` (Rule 6) | references/write.md (see param map below) |
| "validate this person before I attach them" | `formbro validate person --person-id <id> --program-key <key>` | references/write.md |
| "create a new <program> application for <person>" | `formbro applications start --program-key <key> --applicant-id <id>` | references/write.md |
| "patch / update <field> on <entity>" | `formbro <applicants\|applications\|employers\|persons> patch …` | references/write.md |
| "attach / replace / remove <person> from this application" | `formbro applications attach\|replace-person\|remove-person …` | references/write.md |
| "import / create a case from user file(s)" | `formbro import contract --program-key <key>` → local agent reads files and generates JSON → `formbro import apply-json --program-key <key> --json '<json>' --dry-run` → rerun without `--dry-run` | references/write.md |
| "extract/patch data into an existing entity" | `formbro extract contract` then local agent generates JSON then `formbro extract apply-json` | references/write.md |
| "fill the IRCC portal / open browser and fill this case" | `formbro webform start --app-id <id> --program-key <key> --confirmed --headless false` (full signature — see `references/webform.md` skill) | references/webform.md (LOCAL MODE) |
| "preflight / can webform fill this?" | `formbro webform preflight` — portal-adapter dry run only; does NOT re-check data-model validity, run `validate` too before calling a case fill-ready (Rule 6) | references/webform.md |
| "check if my machine can run webform fills" | `formbro webform runtime-check` | references/webform.md |
| "fill the IMM0008 / IMM5257 / IMM5710 PDF" / "give me the filled PDF for case X" | `formbro fill --app-id <id> --forms IMM…,IMM… -o ./out/` | [fill](references/fill.md) (single agent surface; auto-detects TR vs PR; rejects LMIA) |
| "export this applicant / application as Excel / JSON / blank template" | Discover `--form-id` with `formbro programs schema <key> --role <role>`. JSON: `applicants get` / `applications get` / `employers get` then write the file. Excel with data: that JSON into `formbro export data --form-id <FORM> --data '<json>' --output …`. Blank Excel: `formbro export template --form-id <FORM> --output …`. Do not pass schema form ids to `export entity`. | references/write.md |
| "audit / who did what when" | `formbro audit my` | references/read.md |
| "tell Jacky about a bug / feature request / knowledge tip" | `formbro feedback create --type <type> --title "<t>" --description "<d>" [--url <url>] [--context-json '<json>'] [--image <path>]` — draft-confirm before sending | [tell-jacky](references/tell-jacky.md) |

## 1.1 External file intake decision tree

Use this whenever the user gives files: Excel / CSV / Word / DOCX / PDF / screenshots / scans / plain text / email exports.

| User goal | Correct path | Notes |
|---|---|---|
| Create a new applicant + application from file(s) | `import contract` -> local parse -> `import apply-json --dry-run` -> `import apply-json` | This is the canonical new-case import path. The CLI does not read local files; the agent does. |
| Create related people after main import (spouse/dependant/family) | If covered by the import contract, include them there. If not, `persons create` -> `persons patch` -> `applications attach` / `replace-person` -> `validate person` | Use CLI mutations only. |
| Update an existing applicant/application/employer from a file | `extract contract` -> local parse -> `validate data` or dry validation -> `extract apply-json` / `patch` | Use `expected-version` when you just read the entity. |
| Fill IMM PDFs from saved FormBro data | `formbro fill --app-id <id> --forms ... -o <dir>` | Do not parse user PDFs for this; the source data is the saved case. |
| Export saved FormBro entity to Excel / JSON / blank template | `programs schema` then `applicants/applications/employers get` + `export data` / `export template` | Output generation, not import. Do not pass schema form ids to `export entity`. |
| Upload supporting documents / ask what files are needed | `uploads slots` | Current agent surface lists slots; it does not upload arbitrary local files unless a future CLI command adds that. |
| Read a user's Word/PDF/scan to create JSON | Use local document/OCR/spreadsheet tools to extract text/tables, then feed the result into the import/extract JSON contract | Do not claim the CLI itself reads DOCX/PDF/images in the agent-native import path. |

Import boundary rule:
- `import` is the user-facing workflow for creating a new applicant + application from user-provided files.
- The local agent reads Excel/PDF/DOCX/images/CSV/text directly and uses the contract prompt/schema to generate JSON.
- The CLI/backend does not read local files and does not call an LLM in the agent-native import path.
- Do not satisfy a file-import test by cloning an existing DB application or by calling `applications get` as the source of truth.
- Always run `import apply-json --dry-run` before saving. If validation fails, fix the JSON locally and dry-run again.
- `extract` is lower-level and should only patch an existing entity or inspect extraction contracts.

## 1.2 Complete CLI surface, one line per command

This is every public command. Prefer the quick router above; use this list when a user asks for a less common operation. `formbro commands --json` prints the same list with every argument and is the runtime truth when this document and the binary disagree.

**Connect and health**
- `formbro login --token-stdin` — saves the Portal token read from stdin.
- `formbro whoami` — shows the account the token belongs to.
- `formbro health` — takes no arguments; reports whether the backend is reachable.
- `formbro doctor` — reports version, runtime readiness and a live backend check (`--json`, `--no-fetch`, `--fetch`, `--check-upgrade`); `formbro doctor --repair-install` instead reinstalls this package as the only installed copy.
- `formbro commands --json` — prints this CLI's complete public command list as JSON.

**Programs, search and reading**
- `formbro programs list`, `formbro programs describe <program-key>`, `formbro programs schema <program-key> --role <role>` — program metadata and field schemas.
- `formbro find <query>` — free-text search (`--program-key`, `--include`, `--limit`).
- `formbro applicants list`, `formbro applicants get <id>` — read applicants.
- `formbro applications list` (active workbench), `formbro applications inventory` (every status; `--program-key`, `--limit`, `--query`), `formbro applications by-status <status>`, `formbro applications get <id>`, `formbro applications status <id>`, `formbro applications persons <id>`, `formbro applications documents <id>`, `formbro applications resolve --query <text> --program-key <key>` — read applications.
- `formbro employers list`, `formbro employers get <id>` — read LMIA employers.
- `formbro rcic list` — account RCIC profiles (id, names, number, company) with their saved portal credentials.
- `formbro audit my` — the account's own audit trail.

**Intake from files and text**
- `formbro import contract`, `formbro import apply-json` — create a new case from files (`--dry-run` validates only).
- `formbro extract contract`, `formbro extract text`, `formbro extract apply-json` — fill an existing entity from agent-written JSON or text.
- `formbro extract task-status <task-id>`, `formbro extract models`, `formbro extract formats`, `formbro extract clear-cache` — extraction task, model, format and cache helpers.

**Validation**
- `formbro validate data`, `formbro validate by-id`, `formbro validate person`, `formbro validate operation` — check data without saving it.

**Changes**
- `formbro persons create`, `formbro persons patch <id>` — create or edit a person.
- `formbro applicants patch <id>`, `formbro applicants delete <id>` — edit or delete an applicant.
- `formbro applications start`, `formbro applications attach`, `formbro applications replace-person`, `formbro applications remove-person`, `formbro applications set-status`, `formbro applications patch <id>` — create, staff, re-status or edit an application.
- `formbro employers create`, `formbro employers patch <id>`, `formbro employers delete <id>` — manage LMIA employers.
- `formbro notes add` — append a note to an applicant, application or employer.

**Files and PDFs**
- `formbro uploads slots` — document upload slots of a record.
- `formbro fill` — the agent's PDF command: filled IMM PDFs for an application.
- `formbro export entity`, `formbro export data`, `formbro export template` — Excel exports.
- `formbro export pdf`, `formbro export pdf-check`, `formbro export extension` — legacy PDF transport, PDF support probe, browser-extension bundle. Use `export pdf` only when the user explicitly asks for a raw-data PDF preview or a synchronous batch; every ordinary PDF request goes to `fill`.

**Feedback**
- `formbro feedback create` — send a feature request, bug report or knowledge tip; see [tell-jacky](references/tell-jacky.md).

**Local web-form automation**
- `formbro webform runtime-check`, `formbro webform preflight`, `formbro webform start`, `formbro webform jobbank-invite`, `formbro webform status` — check, run and read local portal fills. `webform start` takes `--app-id` or `--query`, `--program-key` and `--confirmed`, plus optional `--case-id`, `--form`, `--new`, `--headless`, `--print-pages` and `--debug-events`.
- `formbro webform daemon start`, `formbro webform daemon stop`, `formbro webform daemon status`, `formbro webform daemon restart`, `formbro webform daemon prune-chromium` — manage the local worker daemon.

**Plugin maintenance (developer only)**
- `formbro pdf bundle`, `formbro pdf verify` — vendor PDF file maintenance; never for a user's PDF request.

## 2. Program key cheat-sheet (do not guess)

| Category | Valid `program-key` values |
|---|---|
| **TR** | `sp-out`, `sp-in`, `wp-out`, `wp-in`, `visa-out`, `visa-in`, `visitor-record` |
| **PR** | `general`, `express-entry`, `caregiver`, `spouse-sponsorship`, `parent-sponsorship`, `renewal` |
| **LMIA** | `hws` (high-wage), `lws` (low-wage), `ee` (Express Entry) |

If unsure of the exact key for a case, run `formbro programs list` first, **never** make up a key like `pr-general-application` or `tr-sp`.

## 3. Entity types — ask the live CLI

Do not memorize or invent form ids / entity-type slugs. Run `formbro programs schema <program-key> --role <role>` (and `programs list` / `--help`) at call time.

- `validate by-id --entity-type` uses the form id returned by `programs schema`.
- Excel / JSON / blank export does **not** use `export entity` with that form id. Live `export entity` rejects schema form ids. Follow [write.md](references/write.md): get JSON, then `export data --form-id` / `export template --form-id`.

## 4. PR / TR / LMIA boundary matrix (which command supports what)

| Command | TR | PR | LMIA | Notes |
|---|:---:|:---:|:---:|---|
| `find`, `programs list`, `audit my` | ✅ | ✅ | ✅ | Cross-program |
| `applicants *`, `persons *` | ✅ | ✅ | ✅ | Person registry is shared |
| `applications start / get / list / status / attach / patch` | ✅ | ✅ | ✅ | Set `--program-key` correctly |
| `applications by-status` | ✅ | ✅ | ✅ | |
| `employers *` | — | — | ✅ | LMIA only — no TR/PR employer entity |
| `validate by-id` (entity_type) | ✅ | ✅ | ✅ | Entity type encodes program |
| `validate person` | ✅ | ✅ | ✅ | Pass `--program-key` |
| `extract *` | ✅ | ✅ | ✅ | Program-aware via `--program-key` |
| `webform start` (LOCAL) | ✅ | ✅ | ✅ | Different IRCC / Service Canada portals |
| `webform preflight / runtime-check / status` | ✅ | ✅ | ✅ | See §6 about status truth |
| **`fill`** (PDF, agent path) | ✅ | ✅ | ❌ | Auto-detects TR vs PR. LMIA explicitly rejected with hint to use `webform start`. See `references/fill.md` skill. |
| `export pdf` (legacy transport) | ✅ | partial | — | TR-route-only in cli; **don't expose to agent** — use `fill`. Kept for raw-data preview / sync batch. |
| `export data` / `export template` (Excel) | ✅ | ✅ | ✅ | `--form-id` from live `programs schema`. Agent Excel path. |

If you call a command in the wrong column, the CLI returns a structured 4xx error with the right alternative — surface that error verbatim to the user, do not retry blindly.

## 5. Execution mode boundary — local vs backend

| Command group | Where it runs | Network |
|---|---|---|
| `find`, `applicants *`, `applications *` (read), `employers *`, `programs *`, `audit my` | **Backend** call (HTTPS to `formbro-api.jackyzhang.app`) | Required |
| `applications` write, `persons` write, `employers` write, `validate *`, `extract *`, `notes add`, `uploads slots` | **Backend** call | Required |
| **`fill`** (PDF, agent path) | **Backend-rendered PDF fill.** The CLI calls the saved-application `fill-pdf` endpoint, saves a returned PDF or safely extracts a returned ZIP, and emits local paths in `files[]`. Existing files are never overwritten. | Required. |
| `export pdf`, `pdf-check`, `export entity / data / template` | **Backend** call (returns binary). `export pdf` is the legacy transport — use `fill` from agent path. | Required |
| **`webform start`** | **LOCAL — spawns a Node + Playwright + Chromium process on the user's machine.** Drives the IRCC / Service Canada portal in a real browser the user can see (with `--headless=false`). | Required (the local browser hits IRCC) |
| `webform preflight`, `webform runtime-check` | LOCAL (no backend call needed for `runtime-check`) | varies |
| **`webform status`** | **Backend** read of last persisted run state — see §6 |

## 6. Status truth model — KNOWN LIMITATION

When a webform fill runs in **local mode**, the **authoritative final status** is one of:

1. The local runner's own exit code + final JSON it printed to stdout. **Trust this first.**
2. What the IRCC portal page itself shows after the run (e.g., "Ready to submit"). The user can confirm this in their browser.

`formbro webform status --app-id …` calls the **backend** and returns whatever was last persisted there. The backend may be:

- ahead (something posted progress mid-run that hasn't synced back)
- **behind** (the local runner finished but never reported back — common after a CLI kill, network hiccup, or backend hot-reload)
- stale from an earlier abandoned run

**Do not treat `webform status` output as the ground truth in local mode.** When the user asks "is the webform fill done?", consult the local runner's last output OR ask the user to confirm what the browser shows. Only if neither is available, fall back to `webform status` — and label it "(may be stale)".

This is a known limitation of `cli-rs` being a thin client without local runner state introspection. Documenting it; not silently working around it.

## 7. Default execution behavior

- If user intent is unambiguous AND no extra parameters are needed AND the operation is **not destructive** (delete, set-status, webform start), **run it directly**. Do not narrate a plan first.
- For **destructive** operations (`employers delete`, `applicants delete`, `applications set-status`, `webform start --confirmed`), confirm with the user once, then run.
- For **ambiguous intent** (multiple plausible programs / entities), ask one specific clarifying question. Do not enumerate every possible interpretation.
- When the CLI returns a structured error with a remediation hint (`"next_required_checks"`, `"hint"`, `"alternative"`), surface it verbatim. The CLI is the source of truth for what to try next.

### 7.1 PARALLELIZE aggressively

The FormBro CLI is **stateless per invocation** — each `formbro <subcommand>` is an independent process with its own HTTPS connection. There is no shared client to bottleneck on, no rate limit headache for normal use. **Run independent calls in parallel whenever you can:**

- **Multi-entity fetches**: when the user asks about a case, fetch the application + each attached person + each related employer **at the same time**. Don't serialize 5 backend calls for "show me everything about case X" — fan them out.
- **Multi-form PDF / Excel exports**: `export pdf` for IMM0008 + IMM5406 + IMM5645 + IMM5669 + IMM5709 should be 5 concurrent invocations, not sequential. Total wall time = slowest single fill, not the sum.
- **Bulk validation**: validating 10 entities → 10 concurrent `validate by-id` calls.
- **Cross-program search**: if the user names a person and you don't know which program their case is in, run `find` in parallel against TR + PR + LMIA scopes.
- **Webform preflight + data fetch**: `webform preflight` + `applications get` + `applications persons` are independent; do them at once.

**Sequencing only when there is a real data dependency.** `validate by-id` then `applications patch` must be serial (validate result drives the patch). Bulk reads do not.

**Use your runtime's parallel-tool-call mechanism** (Codex's parallel tool execution, async batches, `asyncio.gather`, `Promise.all`, or whatever your harness offers). Concretely: emit multiple shell tool calls in a single response message and let the runtime execute them concurrently. The agent that takes 3 seconds for a 5-form export is doing it wrong; the right answer is well under a second.

### 7.2 Concurrency boundary — definitive table

| Group | Default mode | Why |
|---|---|---|
| `find`, `applications get/list/status/by-status`, `employers list/get`, `programs *`, `audit my`, `whoami`, `health` | **PARALLEL** | read-only HTTPS |
| `validate by-id`, `validate person`, `webform preflight`, `webform status`, `webform daemon status`, `doctor` (with or without `--no-fetch`) | **PARALLEL** | read-only / pure check |
| `webform runtime-check` | **SERIAL** (first call) → **PARALLEL** (subsequent) | First call spawns the singleton worker daemon (singleton lock file under `~/.formbro/runtime/`); subsequent calls just health-ping it. Sandboxed environments without `flock`/named-socket support will fail on first call — that's an environment limitation, not a docs bug. |
| `fill` (PDF) — multiple forms in **one** `formbro fill` call | one call — let the backend render the requested set | one authenticated request returns a PDF or ZIP |
| `fill` (PDF) — across **different applications** | **PARALLEL** | independent applications |
| `extract text`, `extract apply-json` (read steps) | **PARALLEL** | independent |
| `notes add`, `uploads slots` (different entities) | **PARALLEL** | independent state |
| `export entity`, `export pdf` (across different applications / entities) | **PARALLEL** | independent fetches |
| `applications patch`, `employers patch`, `persons patch` (mutations on **different** entities) | **PARALLEL** | no shared state |
| `applications patch` on the **same** entity, sequentially with `validate by-id` | **SERIAL** | data dependency |
| `webform start` (local browser + worker daemon) | **SERIAL — ALWAYS** | the worker daemon is a singleton process per user; two concurrent `start` calls fight for the same Unix socket / Chromium instance |
| `webform daemon start/stop/restart/prune-chromium` | **SERIAL** | manage singleton; concurrent calls race |
| `login` (writes `~/.jackyzhang.app/token/user.json`) | **SERIAL** | shared durable token slot |

Rule of thumb: **anything involving the local browser or the local worker daemon is serial. Everything else is parallel.**

If the docs are unclear for a new command, default to PARALLEL for read-only / network-bound subcommands and SERIAL for anything that touches `~/.formbro/runtime/*` (the daemon socket / pid file / Chromium cache).

## 8. Token & secret rules

- **Never log the token value.** Mask any `jz_*` value as `jz_***` in any output.
- The user's token lives only in `~/.jackyzhang.app/token/user.json`. Captured once by [connect](references/connect.md).
- Do not write the token anywhere else, do not include it in example commands, do not echo it back.
