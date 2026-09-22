# Tell Jacky (AnyMail)

**plugin_id:** `anymail` · **aud:** `anymail`

Portal owner-bridge is live on this CLI:

```bash
printf '%s' "$PORTAL_JZ_TOKEN" | anymail login --token-stdin --json
anymail whoami --json
anymail feedback create --type bug-report --title "..." --description "..." --user-confirmed --json
anymail feedback list --json
anymail feedback status --id <id> --json
anymail feedback inbox --json
anymail feedback read --update-id <id> --json
```

Rules:

- Never put the Portal `jz_` on argv, in chat, or in feedback title/description.
- Only pass `--user-confirmed` after the human approved the draft.
- Prefer recoverable product actions first; use Tell-Jacky for product gaps, not mail content dumps.
- Offline / unknown audience falls back to a local mirror under the platform home; still never invent an admin plugin.
