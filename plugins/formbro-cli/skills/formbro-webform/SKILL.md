---
name: formbro-webform
description: LOCAL MODE — runs Playwright + Chromium on the user's machine to fill IRCC / Service Canada portals. Never submits; stops at "Ready to submit". Requires connect-formbro to have been run first.
when_to_use: |-
  Trigger phrases (web portal intent, NOT PDF):
    - "fill the webform for <person>"
    - "open the IRCC portal and fill"
    - "automate the LMIA Job Bank application"
    - "preflight this case for webform fill"
    - "can my machine run a fill / is the worker daemon up"
  NOT for: "fill the PDF" / "give me IMM0008" → use formbro-fill.
---

# FormBro webforms — LOCAL MODE

## Agent happy path (start here)

User says "fill the webform for &lt;person&gt;" → run **one** command:

```bash
formbro webform start-by-name \
  --query "<person name>" \
  --program-key <general|express-entry|spouse-sponsorship|caregiver|sp-out|wp-out|visa-out|...> \
  --confirmed \
  [--headless false]      # add when the user wants to watch a visible browser
```

Behaviour:
- Resolves the name → application id within that program-key.
- Returns `match: "unique"` → runs preflight + start automatically.
- Returns `match: "ambiguous"` → emits `candidates[]` JSON; you pick an id and call `webform start --app-id <id>` directly.
- Returns `match: "none"` → name doesn't exist in that program; ask user to confirm name/program.
- Returns the compact summary `{ok, status, progress, steps_completed, application_id, program_key, elapsed_ms}` on success. Add `--debug-events` to also get the full WebFiller event trace in the response.

**`--headless` default is `true`** — agents running unattended should leave it off. Only pass `--headless false` when there's a human at the keyboard who wants to watch Chromium.

**`--debug-events` sensitivity warning**: passing `--debug-events` attaches the full WebFiller event trace to the response. Events may include in-flight portal field values (applicant data being typed into IRCC inputs) and may include IRCC session identifiers. Treat the output as PII-equivalent: do NOT forward it to third-party tools or paste it into chat with anyone other than the user whose case it is.

**Daemon socket security**: the worker daemon listens on `~/.formbro/runtime/formbro-worker-<user>.sock` (Unix) / per-user named pipe (Windows). On Unix the socket is created mode `0600` and only the owning user can connect. The bundled `formbro` binary talks to it via the `worker_ipc` client. No remote / cross-user attach surface exists. If you see another user's account in the daemon log, that's a serious bug — escalate immediately.

For lower-level control (already-resolved id, manual preflight, etc.) skip below to the **Reference** section.

## ⚠ THIS RUNS ON THE USER'S MACHINE

- `webform start*` spawns Playwright + Chromium **locally**. The browser opens on the user's screen, not in the cloud.
- The fill stops at "Ready to submit". **The CLI never clicks final submit** — the user does.
- If the agent is running on a different machine than the user's desktop (e.g. remote SSH), tell the user this and ask them to invoke from their own desktop.

## Why the first call is slow — DO NOT assume stuck

The first `webform runtime-check` (or first `start*`) on a fresh machine / fresh plugin version takes **20–60 seconds**. This is expected, not a bug. It does two one-time things:

1. **Unpacks the bundled Chromium** into `~/.formbro/runtime/chromium-<rev>/` (one tarball per plugin version).
2. **Starts the long-lived `webform-worker` daemon** — listens on `~/.formbro/runtime/formbro-worker-<user>.sock`.

Subsequent calls reuse the cached Chromium + warm daemon; they respond in ≤2 s.

**Progress signal during slow startup**: the worker emits a log line every ~5 s while initialising. If your harness can tail the daemon log, you'll see steady output.

**What "actually stuck" looks like**:
- No log line for **≥90 s**, AND
- `~/.formbro/runtime/formbro-worker-*.sock` is missing, AND
- `formbro webform daemon status` errors / hangs.

If all three are true, suspect a real bug — run `formbro webform daemon restart`, then re-try. If still stuck, capture `formbro doctor --json` and ask the user to file an issue.

Under 90 s with no socket, just **wait** — it's the one-time unpack.

## Routing table (for non-happy-path intents)

| User intent | Command |
|---|---|
| "fill the webform for <person>" | `webform start-by-name --query <name> --program-key <key> --confirmed` |
| "fill the webform for application <id>" | `webform start --app-id <id> --program-key <key> --confirmed` |
| "is this case ready to fill?" | `webform preflight --app-id <id> --program-key <key>` |
| "can my machine even run webform fills?" | `webform runtime-check` |
| "what's the status of the last fill on case X" | `webform status` — but see **Status truth model** below |
| "resolve a name to an application id" | `applications resolve --query <name> --program-key <key>` |

## Status truth model — KNOWN LIMITATION (read this)

When a webform fill runs in **local mode**, the **authoritative final status** is one of:

1. **The local runner's own exit code + final JSON it printed to stdout.** Trust this first.
2. **What the IRCC portal page itself shows after the run** (e.g., "Ready to submit"). The user can confirm this in their browser.

`<formbro> webform status --app-id …` calls the **backend** and returns whatever was last persisted there. The backend may be:

- ahead (something posted progress mid-run that hasn't synced back),
- **behind** (the local runner finished but never reported back — common after a CLI kill, network hiccup, or backend hot-reload),
- stale from an earlier abandoned run.

**Do not treat `webform status` output as the ground truth in local mode.** When the user asks "is the webform fill done?", consult the local runner's last output OR ask the user to confirm what the browser shows. Only if neither is available, fall back to `webform status` — and label it "(backend-persisted, may be stale)".

This is a known limitation of the `cli-rs` thin client; documenting it, not silently working around it.

## Sequencing rules

1. **First time on a machine:** run `runtime-check`. If Chromium hasn't been fetched yet, the bundled worker downloads it (~336 MB once, into `~/.formbro/runtime/chromium-<rev>/`). Subsequent calls reuse the cache.
2. **Before every `start`:** run `preflight`. It returns missing / blocking validation errors. Do not start a fill that preflight rejects — the run will burn time and abort mid-portal.
3. **Confirm before `start`:** `webform start` opens a browser, navigates to IRCC, and **types into a real portal session**. Confirm with the user once.
4. **Headless default depends on who's driving:**
   - **Interactive user, first fill of a new program** → `--headless false` (visible Chromium so the user can watch).
   - **Agent-driven / unattended / repeat fills** → `--headless true` (no browser window, no user observing). This is the safe default for AI agents that are running tasks without a human at the keyboard.
   - You may always override by asking the user. When in doubt for an unattended run, choose `--headless true` — completion semantics are identical, just no visible window.
5. If a fill is killed mid-run, `webform start` with the same `--app-id` resumes from the last persisted step — no need to start over.

## Bundled worker daemon (v1.3.0+)

As of plugin v1.3.0, the webform pipeline is **fully self-contained**:
- The `formbro` CLI auto-spawns a long-lived `webform-worker` daemon (sibling binary, Bun-compiled).
- Daemon listens on a per-user Unix socket / Windows named pipe (`~/.formbro/runtime/formbro-worker-<user>.sock`).
- BrowserContext is fresh per fill (no cookie leakage); Chromium binary stays warm; daemon idles out after 15 min.
- You do NOT need to install Node or run `~/formbro/desktop`; the worker is bundled.

Daemon management (rarely needed; mostly debugging):

| Command | Use case |
|---|---|
| `formbro webform daemon status` | "Is the daemon running? Which Chromium rev?" |
| `formbro webform daemon stop` | Before uninstalling the plugin; force a clean reload |
| `formbro webform daemon restart` | After upgrading Chromium revision |
| `formbro webform daemon prune-chromium` | Reclaim disk space — deletes non-current Chromium revs |

The worker daemon binary is resolved in this order: `$FB_WORKER_BIN` (env override, for dev) → sibling to `formbro` in the same `bin/<platform>/` directory (production). There is **no** other fallback path; references to a legacy `FORMBRO_WEBFORM_RUNNER` env var in old docs were removed — that variable was never implemented in v1.3.0+.

## Program coverage (which portals are wired)

| Program category | Portal driven | Supported `--program-key` values |
|---|---|---|
| TR | IRCC online (study / work / visitor) | `sp-out`, `sp-in`, `wp-out`, `wp-in`, `visa-out`, `visa-in`, `visitor-record` |
| PR | IRCC PR (general intake / EE / sponsorships) | `general`, `express-entry`, `spouse-sponsorship`, `parent-sponsorship`, `caregiver`, `renewal` |
| LMIA | Service Canada Job Bank + LMIA portals | `hws`, `lws`, `ee` |

For LMIA there are also Job Bank invitations etc. that are LMIA-specific webform flows handled by the same `webform start` (program-key = `hws` / `lws` / `ee`).

## Reference (full subcommand list)

```sh
<formbro> webform runtime-check                                      # local, no --app-id
<formbro> webform preflight  --app-id <id> --program-key <key>
<formbro> webform status     --app-id <id> --program-key <key>       # BACKEND-persisted; see truth model
<formbro> webform start      --app-id <id> --program-key <key> \
                             [--case-id <id>] \
                             --confirmed \
                             [--headless false]
```

## What this skill does NOT do

- Does not click the final IRCC submit button. Ever.
- Does not run server-side. Browser runs on the **user's** machine.
- Does not modify the application's case data. Only reads from FormBro + types into the portal. For data mutations, use `formbro-write`.
- Does not provide a reliable "is the fill done" signal via `webform status` in local mode — see the truth model above.
