---
name: formbro-capabilities
description: READ THIS FIRST. One-page consumption contract for AI agents. Tells you exactly which FormBro skill to call for any user intent, which commands are PR / TR / LMIA only, what runs locally vs in the backend, and which status sources to trust. Read this before guessing parameters or trying commands.
---

# FormBro plugin — agent consumption contract

**Read this once on plugin load.** It tells you, in one page, which skill / subcommand to call for any user intent, and what to never guess.

## 1. Skill router by user intent

| User says (any phrasing) | Call | Skill |
|---|---|---|
| "find / search / look up <person or company>" | `formbro find "<query>"` | formbro-read |
| "what's the status of <case/applicant>" | `formbro applications get <id>` (TR/PR/LMIA all) **OR** `formbro applications status <id>` | formbro-read |
| "list cases for <client>" or "all <program> applications" | `formbro applications list --program <key>` | formbro-read |
| "show me employer <name>" | `formbro employers list --search` then `employers get` | formbro-read |
| "validate this application / can I submit?" | `formbro validate by-id --entity-type <T> --entity-id <id>` | formbro-write (see param map below) |
| "validate this person before I attach them" | `formbro validate person --person-id <id> --program-key <key>` | formbro-write |
| "create a new <program> application for <person>" | `formbro applications start --program-key <key> --applicant-id <id>` | formbro-write |
| "patch / update <field> on <entity>" | `formbro <applicants\|applications\|employers\|persons> patch …` | formbro-write |
| "attach / replace / remove <person> from this application" | `formbro applications attach\|replace-person\|remove-person …` | formbro-write |
| "extract data from this text / document" | `formbro extract text` then `formbro extract apply-json` | formbro-write |
| "fill the IRCC portal / open browser and fill this case" | `formbro webform start --confirmed=true --headless=false` | formbro-webform (LOCAL MODE) |
| "preflight / can webform fill this?" | `formbro webform preflight` | formbro-webform |
| "check if my machine can run webform fills" | `formbro webform runtime-check` | formbro-webform |
| "fill the IMM0008 / IMM5257 / IMM5710 PDF" / "give me the filled PDF for case X" | `formbro fill --app-id <id> --forms IMM…,IMM… -o ./out/` | **formbro-fill** (single agent surface; auto-detects TR vs PR; rejects LMIA) |
| "export this applicant / application as Excel" | `formbro export entity --entity-type <T> --entity-id <id> --output …` | formbro-write |
| "audit / who did what when" | `formbro audit my` | formbro-read |

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
| **`fill`** (PDF, agent path) | **Backend today, local soon.** Output JSON's `engine` field (`backend` or `local`) tells you which. CLI surface stays the same when it flips. | Required today; **post-flip**: only the initial template fetch hits backend (cached afterward). |
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
- For **destructive** operations (`employers delete`, `applicants delete`, `applications set-status`, `webform start --confirmed=true`), confirm with the user once, then run.
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

**Webform fills run on the user's machine** so heavy parallelism there can saturate their CPU — keep webform `start` calls serialized unless the user asks for parallel browser sessions explicitly. All other subcommands (read / write / validate / export / extract) are network-bound and parallelize cleanly.

## 8. Token & secret rules

- **Never log the token value.** Mask any `fb_*` value as `fb_***` in any output.
- The user's token lives only in `~/.formbro/config.json` (or `%USERPROFILE%\.formbro\config.json`). Captured once by `connect-formbro`.
- Do not write the token anywhere else, do not include it in example commands, do not echo it back.
