---
name: connect-formbro
description: One-time setup. Capture the user's FormBro API token (jz_…) and persist it via the bundled CLI. Run this once before any other FormBro skill. After it succeeds, also load formbro-capabilities for the agent consumption contract.
when_to_use: |-
  Trigger phrases:
    - "connect to formbro / set up formbro"
    - "log in to formbro / save my jz_ token"
    - "configure formbro plugin / use this token"
    - first invocation of any formbro skill when ~/.formbro/config.json missing
    - a bare/ambiguous first mention of "formbro" with no other task content — "@formbro", "formbro",
      "hi formbro", "hey formbro", "what can formbro do", "how do I use formbro", "help with formbro" —
      treat any of these, on their own, as an implicit request to get started
---

# Connect FormBro

## Skill load order (resolves any apparent contradiction)

Two different time-scales:
- **`connect-formbro` (THIS skill)** is the **one-time setup ritual** — runs once per user/machine to capture the API token + verify cache freshness. Then it tells you to load `formbro-capabilities`.
- **`formbro-capabilities`** is the **every-session reference contract** — load it (and keep it loaded) for every interaction. Its description says "READ THIS FIRST" because, once setup is done, capabilities is what an agent reads first on each subsequent session.

So: **`connect-formbro` once → then `formbro-capabilities` every session**, including the very first one. Both can be true.

## Already-connected fast path

If `~/.formbro/config.json` exists from a previous session AND `formbro whoami` returns 200, the user is already connected. Skip the token capture (step 1) and the login step (step 4) — **but still run the doctor self-check (step 3)** because plugin upgrades happen out-of-band and you should detect a stale cache on every session start.

## What this does (first-time path)

Persists the user's FormBro API token through the bundled `formbro` CLI so that every subsequent skill (read / write / webform / export) can call the FormBro backend without ever seeing the raw token again. **Never ask the user to paste a token into chat.** The normal human flow is local entry at the CLI's hidden terminal prompt; `FORMBRO_API_TOKEN` remains an explicit automation override.

## How it works

1. Tell the user to generate a FormBro API token at https://jackyzhang.app/account/tokens. The token starts with `jz_`. Never ask the user to reveal it in chat or an agent tool call.

   Note: new tokens default to **read** scope. Most FormBro skills need **write** scope too
   (mutations, imports, and `tell-jacky` feedback submission all fail with a 403 on a read-only
   token). The CLI cannot mint or upgrade a token itself — if the user hits a write-scope 403
   later, send them back to the account Portal at https://jackyzhang.app/account/tokens to edit the token in place (no need to
   regenerate) rather than troubleshooting the CLI.
2. **Resolve the bundled `formbro` binary** — defer to `formbro-capabilities/SKILL.md` §B (the canonical resolver: `$FORMBRO_BIN` → codex cache → claude cache → `command -v`). Set `$FORMBRO_BIN` in the shell once; subsequent commands in every FormBro skill use that. The earlier "read `runtime-manifest.json`" instruction is obsolete and has been replaced by §B's portable resolver.
3. **Plugin cache freshness self-check (mandatory):**

   ```sh
   <BUNDLED_FORMBRO> doctor --json --no-fetch --check-upgrade
   ```

   - `--no-fetch` is **required** — skips bootstrap (which can hit the network + write to the cache dir) and guarantees a pure local cache read.
   - `--check-upgrade` adds one lightweight call to the GitHub Tags API to detect newer plugin releases. Cost: ~200-500 ms. Failure is non-fatal — `upgrade.checked: false` with a `check_failed_reason`. **Surface upgrade warnings to the user when `upgrade.upgrade_available: true`** so they don't hit already-fixed bugs.

   **Failure handling — before parsing JSON:**
   - If the command exits non-zero **OR** prints anything that doesn't parse as JSON: abort the connect flow. Surface the raw output to the user with the message "FormBro CLI doctor check failed — the bundled binary may not be installed correctly. Verify your plugin install and re-run." **Do not proceed to step 4 (login).**

   **Branching on the parsed JSON** (in order — first matching branch wins):

   - `cache.location == "not_in_cache"`: dev install or manual install. Proceed silently.
   - `cache.stale == false`: cache is current. Proceed silently.
   - `cache.stale == true`: tell the user verbatim:

     > Your FormBro plugin cache is stale: running version `<cache.version>` from `<cache.location>` cache, latest available `<cache.latest_available>`. Refresh via your plugin manager (codex: re-sync `jacky-plugins`; claude: reinstall the plugin) before proceeding, or this session may hit known-fixed bugs (Bun JIT OOM on Apple Silicon, action schema mismatch, etc.).
     >
     > If you want to proceed anyway: say so explicitly and I'll continue.

     Wait for user direction before continuing.
   - Any other shape (missing fields, unexpected `cache.location` value): proceed but warn — log "doctor returned unexpected cache shape; continuing".

   **Also branch on `upgrade.upgrade_available`** (only when --check-upgrade ran):

   - `upgrade.upgrade_available == true`: tell the user verbatim:

     > A newer FormBro plugin version (`<upgrade.latest>`) is available; you're on `<upgrade.current>`. New releases often ship bug fixes for issues you might hit. Recommend upgrading via your plugin manager (codex: re-sync `jacky-plugins`; claude: reinstall the plugin) before continuing. Want to upgrade now, or proceed anyway?

     If user says proceed: continue.
   - `upgrade.checked == false`: log "upgrade check failed (reason); proceeding with current version" and continue silently — don't bother the user with network errors.

   This check is cheap (single local filesystem scan; no network IO because of `--no-fetch`) and saves the user from chasing already-fixed bugs across the rest of the session. Do not skip it.

4. Tell the user to run the login command in their own terminal and enter the token at the hidden prompt. The token never enters argv, shell history, chat, or an agent tool request:

   ```sh
   <BUNDLED_FORMBRO> login --token-stdin
   ```

   The user reports only whether it succeeded. Output is JSON: `{"status":"ok","path":"/Users/.../.formbro/config.json"}`. The CLI writes the token + default backend URL to that path. Non-interactive automation may use a governed stdin secret channel or `FORMBRO_API_TOKEN`, but skills must not construct a token-bearing pipe.

5. Verify by running:

   ```sh
   <BUNDLED_FORMBRO> whoami
   ```

   On success: a JSON object with the user's id and email. On failure (`401`): the token is invalid or revoked — direct the user to regenerate one.

6. **Runtime readiness bootstrap (mandatory after successful login / whoami):**

   ```sh
   <BUNDLED_FORMBRO> doctor --json --fetch
   ```

   This prepares the lazy webform worker and verifies the live backend before the
   first real operation. PDF fills are backend-rendered and need no local runtime.
   If this step succeeds, agents should not later need to manually extract
   `webform-worker` or set `FB_WORKER_BIN`. If it fails, surface the
   `runtime_error` / `live.hint` from the JSON and stop before claiming the plugin
   is ready.

## Token rules — never break

- **Never log the token value.** Mask it as `jz_***` in any output you show the user.
- **Never write the token into any file other than the CLI's own `config.json`.** The CLI's stdin login writes that file; do not write your own copy elsewhere.
- **Never embed the token into prompts, tool descriptions, or example commands** you generate. Always use `jz_***` as a placeholder when you show example commands.
- The token is a long-lived bearer credential. If exposed, the user must rotate it at https://jackyzhang.app/account/tokens.

## After this skill succeeds

Tell the user:

> Connected as `<email from whoami>`. FormBro plugin v`<doctor.binary_version>` ready.
>
> **Quick reference** (you can ask me for any of these):
> - "fill the webform for `<person name>`" — one-step: resolve + validate + preflight + fill
> - "find `<person>`'s applications" — name → application ids
> - "is my plugin healthy / which backend am I on" — runs `formbro doctor --json`

Then (silently, no narration) load `formbro-capabilities/SKILL.md`. It tells you:
- Which skill to call for any user intent
- Valid `program-key` / `entity-type` values (do not guess)
- PR / TR / LMIA support matrix per command
- Local vs backend execution boundary
- **Status truth model** — when to trust `webform status` and when not to
