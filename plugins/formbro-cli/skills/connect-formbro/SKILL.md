---
name: connect-formbro
description: One-time setup. Capture the user's FormBro API token (fb_…) and persist it via the bundled CLI. Run this once before any other FormBro skill. After it succeeds, also load formbro-capabilities for the agent consumption contract.
---

# Connect FormBro

This is the **first skill the user must run**. It is the only skill that asks for input.

After this succeeds, also load `formbro-capabilities` (the one-page consumption contract) before doing anything else — it tells you which skill to call for which user intent and saves you from guessing parameters.

## What this does

Persists the user's FormBro API token through the bundled `formbro` CLI so that every subsequent skill (read / write / webform / export) can call the FormBro backend without ever seeing the raw token again.

## How it works

1. Ask the user for their FormBro API token. The token starts with `fb_`. The user gets it from https://formbro.ca → Settings → API Tokens → Create token.
2. Resolve the bundled `formbro` binary by reading `runtime-manifest.json` in the plugin root and looking up `binary.platforms[<platform>-<arch>].path`. Platform values: `darwin-arm64`, `darwin-x64`, `win32-x64`. On macOS, `chmod +x` the binary if not already executable.
3. **Plugin cache freshness self-check (mandatory):**

   ```sh
   <BUNDLED_FORMBRO> doctor --json --no-fetch
   ```

   The `--no-fetch` flag is **required** — it skips bootstrap (which can hit the network + write to the cache dir) and guarantees a pure local read.

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

   This check is cheap (single local filesystem scan; no network IO because of `--no-fetch`) and saves the user from chasing already-fixed bugs across the rest of the session. Do not skip it.

4. Run:

   ```sh
   <BUNDLED_FORMBRO> login --token <USER_TOKEN>
   ```

   Output is JSON: `{"status":"ok","path":"/Users/.../.formbro/config.json"}`. The CLI writes the token + default backend URL to that path.

5. Verify by running:

   ```sh
   <BUNDLED_FORMBRO> whoami
   ```

   On success: a JSON object with the user's id and email. On failure (`401`): the token is invalid or revoked — direct the user to regenerate one.

## Token rules — never break

- **Never log the token value.** Mask it as `fb_***` in any output you show the user.
- **Never write the token into any file other than the CLI's own `config.json`.** That file is what the CLI's `--token` flag writes; do not write your own copy elsewhere.
- **Never embed the token into prompts, tool descriptions, or example commands** you generate. Always use `fb_***` as a placeholder when you show example commands.
- The token is a long-lived bearer credential. If exposed, the user must rotate it at formbro.ca → Settings → API Tokens.

## After this skill succeeds

Tell the user:

> Connected. Reading the FormBro capability contract now.

Then (silently, no narration) load `formbro-capabilities/SKILL.md`. It tells you:
- Which skill to call for any user intent
- Valid `program-key` / `entity-type` values (do not guess)
- PR / TR / LMIA support matrix per command
- Local vs backend execution boundary
- **Status truth model** — when to trust `webform status` and when not to
