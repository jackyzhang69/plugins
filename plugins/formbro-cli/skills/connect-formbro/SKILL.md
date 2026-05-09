---
name: connect-formbro
description: One-time setup. Capture the user's FormBro API token (fb_…) and persist it via the bundled CLI. Run this once before any other FormBro skill.
---

# Connect FormBro

This is the **first skill the user must run**. It is the only skill that asks for input.

## What this does

Persists the user's FormBro API token through the bundled `formbro` CLI so that every subsequent skill (read / write / webform / export) can call the FormBro backend without ever seeing the raw token again.

## How it works

1. Ask the user for their FormBro API token. The token starts with `fb_`. The user gets it from https://formbro.ca → Settings → API Tokens → Create token.
2. Resolve the bundled `formbro` binary by reading `runtime-manifest.json` in the plugin root and looking up `binary.platforms[<platform>-<arch>].path`. Platform values: `darwin-arm64`, `darwin-x64`, `win32-x64`. On macOS, `chmod +x` the binary if not already executable.
3. Run:

   ```sh
   <BUNDLED_FORMBRO> login --token <USER_TOKEN>
   ```

   Output is JSON: `{"status":"ok","path":"/Users/.../.formbro/config.json"}`. The CLI writes the token + default backend URL to that path.

4. Verify by running:

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

> Connected. You can now use the FormBro skills: `formbro-read`, `formbro-write`, `formbro-webform`.
