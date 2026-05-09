---
name: formbro-webform
description: Run preflight checks and start local Playwright-driven IRCC / Service Canada portal fills. Local execution only — never submits. Requires connect-formbro to have been run first.
---

# FormBro webforms

The FormBro CLI drives **local** Playwright/Chromium against IRCC (TR/PR) or Service Canada (LMIA). The runtime is on the user's machine — not the FormBro backend. The agent prepares + fills, but **the user always clicks the final submit**.

All commands shell out to the bundled `formbro` CLI (resolve via `runtime-manifest.json`).

## Sanity-check the local runtime

```sh
<formbro> webform runtime-check
```

Exits 0 if Playwright + Chromium are usable on this machine. Run this first if anything below fails.

## Preflight an application

```sh
<formbro> webform preflight --app-id <id> --program-key <key>
```

Returns a JSON readiness report (missing fields, blocking validation errors, warnings). Run this **before** start.

## Status of a running / finished webform fill

```sh
<formbro> webform status --app-id <id> --program-key <key>
```

## Start a webform fill — the actual fill, confirm first

```sh
<formbro> webform start --app-id <id> --program-key <key> \
                        [--case-id <id>] \
                        --confirmed=true \
                        [--headless=false]
```

**Critical rules:**

- `--confirmed=true` is **required** to actually start. Without it, the CLI refuses to launch the browser. Always ask the user to confirm before passing this flag.
- Default `--headless=true`. On the first fill of a new program, pass `--headless=false` so the user can watch what's happening.
- The fill stops when the portal page reaches "Ready to submit" / equivalent. The CLI does not click submit.
- If the run is killed mid-fill, re-running with the same `--app-id` resumes from the last persisted step.

## What this skill does NOT do

- Does not click the final IRCC submit button. Ever.
- Does not run server-side. Browser runs on the user's machine.
- Does not modify the application's case data. Only reads + types into the portal. For data mutations, use `formbro-write`.
