---
name: formbro-fill
description: Agent-facing entrypoint for filling IRCC IMM PDF forms (IMM0008 / IMM5257 / IMM5645 / IMM5709 / etc.). Use this skill for ANY user intent that mentions "fill PDF / IMM form / generate the IMM5257 / give me the filled PDF". Auto-detects TR vs PR from the application; rejects LMIA (which is webform-only). Stable agent surface — local engine for all 13 IRCC forms, no backend round-trip for fill itself.
when_to_use: |-
  Trigger phrases (PDF intent, NOT webform):
    - "fill IMM0008 / IMM5257 / IMM5710 / IMM5406 / etc."
    - "give me the filled PDFs for case X"
    - "generate the IMM forms for <person>'s application"
    - "I need IMM5645 + IMM5710 for <client>"
  NOT for: "fill the webform" / "open the portal" → use formbro-webform.
---

# Fill IRCC IMM PDFs

This is the **only** agent-facing PDF skill. If a user says any of:

- "fill the IMM0008 / IMM5257 / IMM5645 / IMM5710 PDF"
- "generate the filled IMM forms for this case"
- "give me the PDF for application X"
- "I need IMM5257 + IMM5645 for this client"

…run `formbro fill` (defined below). Do **not** route to `formbro export pdf` — that's the legacy underlying transport, not the agent surface.

## Quick router (user intent → exact command)

| If the user says… | Run |
|---|---|
| "fill IMM5257 + IMM5645 for case X" | `<formbro> fill --app-id <id> --forms IMM5257,IMM5645 -o ./out/` |
| "generate the PDF for this PR application" | `<formbro> fill --app-id <id> --forms IMM0008,IMM5406 -o ./out/` |
| "I'm not sure which forms — what's available?" | `<formbro> programs schema <key> --role applicant`, then re-run `fill` with the correct list |
| "fill the LMIA paperwork" | **Reject.** LMIA has no PDF flow — say "LMIA submissions go through `formbro webform start`" and stop. |

## What this command does (and what it does NOT)

- ✅ One stable command for TR + PR PDF fills. **No `--program <category>` argument** — TR vs PR is detected from the application.
- ✅ Output is JSON with a `files` array (each with `path`, `form`, `size_bytes`) plus an `engine` marker. All 13 IRCC forms (incl. IMM0008) ship with a local mapping; `engine` reports `local` for the fill itself. Backend is still hit for the per-application data payload.
- ✅ Multi-form requests are returned as individual `IMM<id>.pdf` files in the chosen output directory (no opaque ZIP for the agent to unpack).
- ❌ Does **not** ask the user about TR vs PR. Pass `--program-key` only if auto-detection fails or the application doc is unusual.
- ❌ Does **not** support LMIA. LMIA is webform-only (handled by `formbro-webform`).
- ❌ Does **not** click submit on anything. PDFs are local files for the user to review and submit themselves.

## Form coverage cheat-sheet

If you don't know which forms a program needs, run `formbro programs describe <key>` first.

| Program category | Common form set (start here) |
|---|---|
| TR study permit (`sp-out`, `sp-in`) | `IMM1294` (out) / `IMM1295` (in), `IMM5645`, `IMM5709`, `IMM5710` |
| TR work permit (`wp-out`, `wp-in`) | `IMM1295` (in) / `IMM5710` (in renewal), `IMM5645`, `IMM5709` |
| TR visitor visa (`visa-out`, `visa-in`) | `IMM5257`, `IMM5645`, `IMM5709` |
| TR visitor record (`visitor-record`) | `IMM5708`, `IMM5645` |
| PR general (`general`) | `IMM0008`, `IMM5406`, `IMM5562`, `IMM5669` |
| PR express entry (`express-entry`) | `IMM0008`, `IMM5406`, `IMM5562`, `IMM5669` |
| PR spouse / parent sponsorship | `IMM0008`, `IMM5406`, `IMM5532` (sponsorship), `IMM1344` (sponsor undertaking) |
| LMIA (`hws`/`lws`/`ee`) | **n/a — webform only** |

If the user asks for a form that the program doesn't support, the CLI returns a structured 4xx with the supported set — surface that error verbatim.

## Output shape (what your runtime sees)

```json
{
  "ok": true,
  "engine": "local",
  "engine_detail": {
    "binary": "/.../plugins/formbro-cli/bin/darwin-arm64/pdf-fill-xfa",
    "vendor_root": "/.../plugins/formbro-cli/assets/pdffiller"
  },
  "category": "tr",
  "program_key": "sp-out",
  "app_id": "abc123",
  "files": [
    { "path": "./out/IMM1294.pdf", "form": "1294", "size_bytes": 683949, "local_ms": 5 },
    { "path": "./out/IMM5645.pdf", "form": "5645", "size_bytes": 1592349, "local_ms": 6 }
  ],
  "elapsed_ms": 312
}
```

Since v1.4.x, **all 13 IRCC forms (IMM0008 included) fill via the local Rust XFA path**. The backend is hit only to deliver the per-application datasets XML (~200–400 ms); the actual byte mutation runs on the user's machine. `engine: "backend"` should not appear under normal operation with a current plugin; if it does, it's a sign of an old cached plugin (refresh via the `formbro doctor --json --no-fetch` ritual in `connect-formbro`).

Treat the contract — filled PDFs at `files[].path` with `ok: true` — as the source of truth. The `engine` field is for telemetry only.

## Status truth model

PDF fills are **synchronous** — when `formbro fill` returns 0, the files at `files[].path` are the result. There is no separate "status" command for PDF fill. (This is unlike `webform start`, which is long-running and has the stale-status problem; PDF fill does not.)

## Parallelism

Each `formbro fill` invocation is independent. If a user asks for, say, "fill IMM0008 + IMM5406 + IMM5562 + IMM5669 for this case", you have two choices:

1. **One call, multiple forms** (preferred): `formbro fill --app-id <id> --forms IMM0008,IMM5406,IMM5562,IMM5669 -o ./out/`. Backend builds them all in one transaction. This is what you should do by default.
2. **Multiple calls, one form each, in parallel**: only if the user is asking for several DIFFERENT applications. Then fan out concurrently — see `formbro-capabilities` §7.1.

Do not serialize per-form calls for a single application. That's the worst pattern.

## Reference (full subcommand)

```sh
<formbro> fill --app-id <id> --forms IMM<id>,IMM<id>,... [-o <dir>] [--program-key <key>] [--engine auto|local|backend]
```

- `--app-id` (required) — application id
- `--forms` (required) — comma-separated list, `IMM` prefix optional (`IMM5257` ≡ `5257`)
- `-o, --output` (default `.`) — directory to write PDFs into; created if missing
- `--program-key` (auto-detected) — TR keys: `sp-out`, `sp-in`, `wp-out`, `wp-in`, `visa-out`, `visa-in`, `visitor-record`. PR keys: `general`, `express-entry`, `caregiver`, `spouse-sponsorship`, `parent-sponsorship`, `renewal`. LMIA keys are rejected.
- `--engine` (default `auto`) — `auto`: use local Rust XFA filler when bundled and all requested forms have a local mapping, otherwise backend. `local`: force local; errors if any form lacks a mapping (currently IMM0008 must use backend). `backend`: force backend round-trip (slower; useful for parity testing).

## Forms that fill locally (no backend round-trip beyond the data fetch)

`0008, 0104, 1294, 1295, 1344, 5257, 5476, 5532, 5645, 5669, 5708, 5709, 5710` — **all 13 IRCC PR/TR forms**. The bundled vendor pack ships pre-flattened PDF templates + dataset-path mappings so no qpdf, no Node, no Python is needed at runtime. Per-form fill takes ~5-10 ms (IMM0008 included).

The IMM0008 mapping is bootstrapped from the backend builder's authoritative field-path table (`backend/services/pdf_filling/imm0008_xfa.py: MAIN_FIELD_PATHS + DEPENDANT_FIELD_PATHS` → 135 fields total: 78 main + 57 dependant rows under `dependants[0].*`). For applications with multiple dependants, the current build covers the first dependant row; multi-row support arrives when the backend payload pushes additional rows.

## What this skill replaces (for agent purposes)

| Legacy `formbro export` command | Why agents should use `formbro fill` instead |
|---|---|
| `export pdf` | Required `--program <key>` AND was TR-route-only in cli; agents that called it for PR cases got 404. `fill` auto-detects category and routes to the right backend endpoint. |
| `export pdf-async` + `export pdf-status` + `export pdf-result` | Async flow has no agent-visible benefit — synchronous fill returns the file in seconds. Async is reserved for future giant-batch use cases; not part of the agent contract. |
| `export pdf-check` | Optional pre-flight; `fill` will surface the same validation errors directly when it actually fills. Skip the round-trip. |

If you genuinely need the async flow (e.g., the user explicitly asked for a background task), `formbro export pdf-async` still works — but that's an explicit user choice, not the default agent path.

## Version

This skill assumes:
- `formbro-cli` ≥ `0.2.0` (introduces the `fill` subcommand)
- Plugin runtime-manifest declares the bundled `formbro` binary

If `formbro fill` errors with "unknown subcommand", the user has an older bundled binary — tell them to reinstall the plugin to pick up the latest CLI.
