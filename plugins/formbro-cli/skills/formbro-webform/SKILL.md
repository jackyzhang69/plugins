---
name: formbro-webform
description: LOCAL MODE — runs Playwright + Chromium on the user's machine to fill IRCC / Service Canada portals. Never submits; stops at "Ready to submit". Requires connect-formbro to have been run first.
---

# FormBro webforms — LOCAL MODE

> ## ⚠ THIS RUNS ON THE USER'S MACHINE
>
> `webform start` is **not** a backend job. It spawns Playwright + Chromium **locally** and drives the IRCC / Service Canada portal in a real browser the user can see (with `--headless=false`).
>
> - The browser opens on the **user's screen**, not in the cloud.
> - The fill stops at "Ready to submit" / equivalent. **The CLI never clicks final submit** — the user does.
> - If the user is on a different machine than where the agent is running (e.g., remote SSH session), tell them this fact and ask them to run from their own desktop.

## Quick router (user intent → exact command)

| If the user says… | Run |
|---|---|
| "fill the IRCC portal for this case" / "open browser and fill this case" | `<formbro> webform start --app-id <id> --program-key <key> --confirmed --headless false` (CONFIRM first) |
| "preflight this case for webform fill" / "is this case ready to fill?" | `<formbro> webform preflight --app-id <id> --program-key <key>` |
| "can my machine even run webform fills?" | `<formbro> webform runtime-check` (no --app-id needed) |
| "what's the status of the webform fill on case X" | See **Status truth model** below — `webform status` is unreliable in local mode |

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
4. **Set `--headless=false` on the first fill of a new program** so the user can watch what's happening; flip to true once they trust it.
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

The `FORMBRO_WEBFORM_RUNNER` env-var override (legacy from pre-v1.3.0) is **deprecated** and will be removed in v1.4.0; it falls back to running the old `.cjs` runner only if the bundled worker binary is missing.

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
