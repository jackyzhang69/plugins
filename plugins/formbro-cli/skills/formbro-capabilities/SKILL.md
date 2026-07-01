---
name: formbro-capabilities
description: READ THIS FIRST. One-page consumption contract for AI agents. Tells you exactly which FormBro skill to call for any user intent, how to verify install/upgrade health, which commands are PR / TR / LMIA only, what runs locally vs in the backend, and which status sources to trust. Read this before guessing parameters or trying commands.
---

# FormBro plugin — agent consumption contract

**Read this once on plugin load and reload it whenever a user asks anything FormBro-related.** It tells you which skill / subcommand to call for each user intent, and what to never guess.

Use when: load on plugin start; reload whenever a user asks anything FormBro-related. Trigger phrases include "fill the webform for X", "find X case", "list applications", "PDF for case X", "is this ready to submit", "check formbro status", "my token / which backend", and "install/update/upgrade FormBro plugin".

## 0. Non-negotiable operating rules

1. **All FormBro system operations go through the bundled CLI.** Reads, creates, patches, attaches, deletes, validation, PDF generation, Excel export, uploads, notes, audit, and webform automation must use `<formbro> ...`. Do not call FormBro backend endpoints directly and do not write to MongoDB / any database directly.
2. **Local file parsing is allowed only before the CLI boundary.** The agent may read Excel, CSV, Word/DOCX, PDF, image, email, or text files locally to extract facts. The moment data is validated, saved, patched, exported, or filled, call the CLI.
3. **For new cases from external files, use `import`, not ad hoc cloning.** Ask FormBro for the contract/schema with `formbro import contract`, generate JSON locally, run `import apply-json --dry-run`, then rerun without `--dry-run` only when valid.
4. **For existing entities, use `extract` / `validate` / `patch`.** Do not use the new-case import path to mutate an existing applicant/application/employer.
5. **Never guess program keys, entity types, or command flags.** Use `programs list`, `programs describe`, `programs schema`, and `--help`. Current flags use `--program-key`, not legacy `--program`.

## 0.1 Install / upgrade acceptance protocol — do this before declaring success

Use this section whenever the user asks to install, update, upgrade, refresh, debug, or verify the FormBro plugin. Do not make future agents rediscover this from trial and error.

1. **Resolve the bundled binary first** using §B. Prefer `$FORMBRO_BIN`, then the Codex plugin cache, then the Claude plugin root, and only then ambient `PATH`.
2. **Run the authoritative health check** with the resolved binary: `formbro doctor --json --no-fetch --check-upgrade`. This is the acceptance check for plugin freshness and runtime health.
3. **Do not trust `codex plugin list` alone.** It reports the marketplace package surface. The bundled CLI runtime can drift behind or ahead; `binary_version`, `cache.stale`, and `runtime_error` from `doctor` are the runtime truth.
4. **Success criteria:** the resolved binary runs; `runtime_error` is `null`; `cache.stale` is `false`; `binary_version` matches the expected signed runtime release; and any upgrade check either succeeds or is explicitly reported as blocked by network.
5. **Network boundary:** if `--check-upgrade` cannot reach GitHub/API because the current sandbox has no network, do not call the install broken. Say remote freshness is unverified, then still report the local `binary_version`, `cache.version`, and `cache.stale` result.
6. **Version boundary:** plugin package version (`.codex-plugin/plugin.json`) is independent from CLI binary version. Docs/skills may bump the plugin package without changing `runtime-manifest.json`, release tag, checksums, or `binary_version`. Change binary metadata only when signed binaries actually changed.
7. **If doctor fails or shows drift:** reinstall from the configured marketplace with `codex plugin add formbro-cli@jacky-plugins`; rerun doctor. If it still fails, inspect `runtime-manifest.json` and signed release assets before editing docs or blaming FormBro data.
8. **After changing plugin source:** bump the plugin package version, validate the plugin, reinstall it locally, then start a new Codex thread before testing new skills. Current threads may keep old loaded skill text.
9. **Codex PATH alias warning:** `WARNING: proceeding, even though we could not create PATH aliases` comes from Codex CLI trying to write `CODEX_HOME/tmp/arg0` under sandbox restrictions. It is not a FormBro backend, token, CLI binary, or marketplace error. Fix Codex writable roots/alias directory or run the plugin manager outside that sandbox; do not rotate tokens or debug applications for this warning.

## Agent quick router — TOP 20 LINES (read this first)

User said this → call this exact command (binary resolution: §B; full router with TR/PR/LMIA detail: §1):

| User intent | Command (one-hop preferred) |
|---|---|
| "create/import a case from Excel/Word/PDF/files" | `formbro import contract --program-key <key>` -> local agent parses files to JSON -> `formbro import apply-json --program-key <key> --json '<json>' --dry-run` -> rerun without `--dry-run` |
| "patch this existing case/person from a file" | `formbro extract contract --program-key <key> --entity-type <T>` -> local agent parses file to JSON -> validate -> `formbro extract apply-json ...` or `persons/applications/employers patch` |
| "fill the webform for `<person>`" | `formbro webform start --query "<person>" --program-key <key> --confirmed` (v1.5.1+ unified; --query OR --app-id) |
| "invite Job Bank candidates for posting `<id>`" | `formbro webform jobbank-invite --job-post-id <id> --rcic-id <rcic_id> --minimum-stars 2\|4 [--max-invites 3] --confirmed` |
| "fill PDF / IMM forms for `<person>`'s case" | `formbro fill --app-id <id> --forms IMM…,IMM…` (see formbro-fill) |
| "find / look up `<person>`" | `formbro find "<person>" --include applications --limit 10` |
| "list ALL cases / inventory / audit sweep" | `formbro applications inventory [--program-key <key>]` (v1.5.0+; returns every status incl. empty) |
| "list my active workbench" | `formbro applications list [--program-key <key>]` (dashboard semantics: only active statuses) |
| "is this case ready to submit" | `formbro webform preflight --app-id <id> --program-key <key>` |
| "is my plugin healthy / which backend am I on / token still valid" | `formbro doctor --json` (live whoami + backend round-trip) |
| "can my machine even run a fill" | `formbro webform runtime-check` |
| "the daemon is acting weird" | `formbro webform daemon status` → `daemon restart` |
| "plugin out of date?" | `formbro doctor --check-upgrade` |

Two semantic distinctions to NEVER conflate:
- `applications list` = consultant's active workbench (dashboard scope; filters by active status)
- `applications inventory` = every application in the account regardless of state (audit / batch / debugging)

Routing detail below is supplementary — start with this 20-line table.

## §B. Resolving the `formbro` binary

The plugin ships a Rust CLI binary that is NOT placed on `PATH` automatically by either Codex or Claude Code. Throughout these skills, `<formbro>` or the literal token `formbro` mean **"the bundled binary at this resolved path"**, not a `PATH` lookup.

**Resolution order (use the first that resolves to an existing executable):**

1. **`$FORMBRO_BIN`** — explicit override. Honor if set.
2. **Codex plugin cache** — `$HOME/.codex/plugins/cache/jacky-plugins/formbro-cli/<version>/bin/<platform>/formbro` where `<version>` is the highest version dir present and `<platform>` matches a bundled OS/arch. Current public marketplace bundles ship `darwin-arm64` and `win32-x64`.
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
# Current public POSIX bundle includes darwin-arm64 only; Windows agents use
# the PowerShell resolver below for win32-x64.
case "$(uname -s)-$(uname -m)" in
  Darwin-arm64)  PLAT=darwin-arm64 ;;
  *) PLAT="" ;;
esac

# Walk a search list in priority order; pick first existing executable.
FORMBRO_BIN=""
_cand_paths=(
  "${CLAUDE_PLUGIN_ROOT:+${PLAT:+$CLAUDE_PLUGIN_ROOT/bin/$PLAT/formbro}}"
)
# Codex cache may have several version dirs; agent picks the *highest* one.
# We use python sort (universally available) — POSIX `sort -V` is not portable.
_codex_root="$HOME/.codex/plugins/cache/jacky-plugins/formbro-cli"
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
  echo "FormBro CLI not found on this host. Current public bundles support darwin-arm64 (and win32-x64 via PowerShell). Install a platform-specific CLI and set FORMBRO_BIN if needed." >&2
  return 1 2>/dev/null || exit 1
fi
export FORMBRO_BIN
"$FORMBRO_BIN" --help >/dev/null || { echo "FormBro CLI at $FORMBRO_BIN is not runnable" >&2; return 1 2>/dev/null || exit 1; }
```

**Windows PowerShell variant** (codex on Windows installs to `$env:USERPROFILE\.codex\...`):

```powershell
$plat = "windows-x64"
$cands = @($env:FORMBRO_BIN_OVERRIDE)
if ($env:CLAUDE_PLUGIN_ROOT) { $cands += "$env:CLAUDE_PLUGIN_ROOT\bin\$plat\formbro.exe" }
$codexRoot = "$env:USERPROFILE\.codex\plugins\cache\jacky-plugins\formbro-cli"
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
| "find / search / look up <person or company>" | `formbro find "<query>"` | formbro-read |
| "what's the status of <case/applicant>" | `formbro applications get <id>` (TR/PR/LMIA all) **OR** `formbro applications status <id>` | formbro-read |
| "list cases for <client>" or "all <program> applications" | `formbro applications list --program-key <key>` | formbro-read |
| "show me employer <name>" | `formbro employers list --search` then `employers get` | formbro-read |
| "validate this application / can I submit?" | `formbro validate by-id --entity-type <T> --entity-id <id>` | formbro-write (see param map below) |
| "validate this person before I attach them" | `formbro validate person --person-id <id> --program-key <key>` | formbro-write |
| "create a new <program> application for <person>" | `formbro applications start --program-key <key> --applicant-id <id>` | formbro-write |
| "patch / update <field> on <entity>" | `formbro <applicants\|applications\|employers\|persons> patch …` | formbro-write |
| "attach / replace / remove <person> from this application" | `formbro applications attach\|replace-person\|remove-person …` | formbro-write |
| "import / create a case from user file(s)" | `formbro import contract --program-key <key>` → local agent reads files and generates JSON → `formbro import apply-json --program-key <key> --json '<json>' --dry-run` → rerun without `--dry-run` | formbro-write |
| "extract/patch data into an existing entity" | `formbro extract contract` then local agent generates JSON then `formbro extract apply-json` | formbro-write |
| "fill the IRCC portal / open browser and fill this case" | `formbro webform start --app-id <id> --program-key <key> --confirmed --headless false` (full signature — see `formbro-webform` skill) | formbro-webform (LOCAL MODE) |
| "preflight / can webform fill this?" | `formbro webform preflight` | formbro-webform |
| "check if my machine can run webform fills" | `formbro webform runtime-check` | formbro-webform |
| "fill the IMM0008 / IMM5257 / IMM5710 PDF" / "give me the filled PDF for case X" | `formbro fill --app-id <id> --forms IMM…,IMM… -o ./out/` | **formbro-fill** (single agent surface; auto-detects TR vs PR; rejects LMIA) |
| "export this applicant / application as Excel" | `formbro export entity --entity-type <T> --entity-id <id> --output …` | formbro-write |
| "audit / who did what when" | `formbro audit my` | formbro-read |

## 1.1 External file intake decision tree

Use this whenever the user gives files: Excel / CSV / Word / DOCX / PDF / screenshots / scans / plain text / email exports.

| User goal | Correct path | Notes |
|---|---|---|
| Create a new applicant + application from file(s) | `import contract` -> local parse -> `import apply-json --dry-run` -> `import apply-json` | This is the canonical new-case import path. The CLI does not read local files; the agent does. |
| Create related people after main import (spouse/dependant/family) | If covered by the import contract, include them there. If not, `persons create` -> `persons patch` -> `applications attach` / `replace-person` -> `validate person` | Use CLI mutations only. |
| Update an existing applicant/application/employer from a file | `extract contract` -> local parse -> `validate data` or dry validation -> `extract apply-json` / `patch` | Use `expected-version` when you just read the entity. |
| Fill IMM PDFs from saved FormBro data | `formbro fill --app-id <id> --forms ... -o <dir>` | Do not parse user PDFs for this; the source data is the saved case. |
| Export saved FormBro entity to Excel | `export entity` | This is output generation, not import. |
| Upload supporting documents / ask what files are needed | `uploads slots` | Current agent surface lists slots; it does not upload arbitrary local files unless a future CLI command adds that. |
| Read a user's Word/PDF/scan to create JSON | Use local document/OCR/spreadsheet tools to extract text/tables, then feed the result into the import/extract JSON contract | Do not claim the CLI itself reads DOCX/PDF/images in the agent-native import path. |

Import boundary rule:
- `import` is the user-facing workflow for creating a new applicant + application from user-provided files.
- The local agent reads Excel/PDF/DOCX/images/CSV/text directly and uses the contract prompt/schema to generate JSON.
- The CLI/backend does not read local files and does not call an LLM in the agent-native import path.
- Do not satisfy a file-import test by cloning an existing DB application or by calling `applications get` as the source of truth.
- Always run `import apply-json --dry-run` before saving. If validation fails, fix the JSON locally and dry-run again.
- `extract` is lower-level and should only patch an existing entity or inspect extraction contracts.

## 1.2 Complete CLI surface by responsibility

This is the full agent-facing command surface. Prefer the quick router above; use this table when a user asks for a less common operation.

| Responsibility | Commands |
|---|---|
| Connect / health | `login`, `whoami`, `health`, `doctor` |
| Program metadata | `programs list`, `programs describe`, `programs schema` |
| Search / read | `find`, `applicants list/get`, `applications list/inventory/by-status/get/status/persons/documents/resolve`, `employers list/get`, `audit my` |
| New-case import from files | `import contract`, `import apply-json` |
| Existing-entity extraction / patch intake | `extract contract`, `extract text`, `extract apply-json`, `extract task-status/models/formats/clear-cache` |
| Validation | `validate data`, `validate by-id`, `validate person`, `validate operation` |
| Mutations | `persons create/patch`, `applicants patch/delete`, `applications start/attach/replace-person/remove-person/set-status/patch`, `employers create/patch/delete`, `notes add` |
| File slots / output | `uploads slots`, `export entity/data/template/pdf/pdf-async/pdf-status/pdf-result/pdf-check/extension`, `fill` |
| Local portal automation | `webform runtime-check/preflight/start/jobbank-invite/status`, `webform daemon start/stop/status/restart/prune-chromium` |
| Plugin/vendor maintenance | `pdf bundle`, `pdf verify` |

Treat `main.rs` + `<formbro> --help` as the runtime truth when docs and code drift.

## 2. Program key cheat-sheet (do not guess)

| Category | Valid `program-key` values |
|---|---|
| **TR** | `sp-out`, `sp-in`, `wp-out`, `wp-in`, `visa-out`, `visa-in`, `visitor-record` |
| **PR** | `general`, `express-entry`, `caregiver`, `spouse-sponsorship`, `parent-sponsorship`, `renewal` |
| **LMIA** | `hws` (high-wage), `lws` (low-wage), `ee` (Express Entry) |

If unsure of the exact key for a case, run `formbro programs list` first, **never** make up a key like `pr-general-application` or `tr-sp`.

## 3. Entity-type cheat-sheet (`validate by-id`, `export entity`)

`entity_type` is `<category-lc>-<program-key>-<role>`:

- TR examples: `tr-sp-in-applicant`, `tr-wp-out-applicant`, `tr-visa-in-applicant`, `tr-sp-in-application`
- PR examples: `pr-general-applicant`, `pr-express-entry-applicant`, `pr-general-application`, `pr-spouse-sponsorship-sponsor`
- LMIA examples: `lmia-hws-employer`, `lmia-lws-employer`, `lmia-ee-employer`, `lmia-hws-application`

Roles vary per program (applicant / spouse / sponsor / employer / dependant / application). When in doubt, run `formbro programs schema <program-key> --role <role>` to see what the registry expects.

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
| **`fill`** (PDF, agent path) | ✅ | ✅ | ❌ | Auto-detects TR vs PR. LMIA explicitly rejected with hint to use `webform start`. See `formbro-fill` skill. |
| `export pdf` (legacy transport) | ✅ | partial | — | TR-route-only in cli; **don't expose to agent** — use `fill`. Kept for raw-data preview / async-batch. |
| `export entity` (Excel) | ✅ | ✅ | ✅ | Per-entity Excel export |

If you call a command in the wrong column, the CLI returns a structured 4xx error with the right alternative — surface that error verbatim to the user, do not retry blindly.

## 5. Execution mode boundary — local vs backend

| Command group | Where it runs | Network |
|---|---|---|
| `find`, `applicants *`, `applications *` (read), `employers *`, `programs *`, `audit my` | **Backend** call (HTTPS to `backend.formbro.ca`) | Required |
| `applications` write, `persons` write, `employers` write, `validate *`, `extract *`, `notes add`, `uploads slots` | **Backend** call | Required |
| **`fill`** (PDF, agent path) | **Local-first PDF fill.** Standard bundled PR/TR PDFs, including IMM5669, fill locally; backend fallback is an exception path for runtime failure or genuinely unsupported future forms. Treat `ok: true` + `files[]` as success; repeated `engine=backend` is a readiness signal. | Required for payload fetch and backend fallback. |
| `export pdf`, `pdf-async`, `pdf-status`, `pdf-result`, `pdf-check`, `export entity / data / template` | **Backend** call (returns binary or task id). `export pdf` is the legacy transport — use `fill` from agent path. | Required |
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
| `fill` (PDF) — multiple forms in **one** `formbro fill` call | already parallel internally — let the CLI do it | local Rust filler stages forms concurrently |
| `fill` (PDF) — across **different applications** | **PARALLEL** | independent applications |
| `extract text`, `extract apply-json` (read steps) | **PARALLEL** | independent |
| `notes add`, `uploads slots` (different entities) | **PARALLEL** | independent state |
| `export entity`, `export pdf` (across different applications / entities) | **PARALLEL** | independent fetches |
| `applications patch`, `employers patch`, `persons patch` (mutations on **different** entities) | **PARALLEL** | no shared state |
| `applications patch` on the **same** entity, sequentially with `validate by-id` | **SERIAL** | data dependency |
| `webform start` (local browser + worker daemon) | **SERIAL — ALWAYS** | the worker daemon is a singleton process per user; two concurrent `start` calls fight for the same Unix socket / Chromium instance |
| `webform daemon start/stop/restart/prune-chromium` | **SERIAL** | manage singleton; concurrent calls race |
| `login` (writes `~/.formbro/config.json`) | **SERIAL** | shared writeable config file |

Rule of thumb: **anything involving the local browser or the local worker daemon is serial. Everything else is parallel.**

If the docs are unclear for a new command, default to PARALLEL for read-only / network-bound subcommands and SERIAL for anything that touches `~/.formbro/runtime/*` (the daemon socket / pid file / Chromium cache).

## 8. Token & secret rules

- **Never log the token value.** Mask any `fb_*` value as `fb_***` in any output.
- The user's token lives only in `~/.formbro/config.json` (or `%USERPROFILE%\.formbro\config.json`). Captured once by `connect-formbro`.
- Do not write the token anywhere else, do not include it in example commands, do not echo it back.
