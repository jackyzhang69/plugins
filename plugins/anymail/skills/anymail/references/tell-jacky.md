# Tell Jacky (AnyMail)

**plugin_id:** `anymail` · **aud:** `anymail`

Portal owner-bridge is live on this CLI:

```bash
printf '%s' "$PORTAL_JZ_TOKEN" | anymail login --token-stdin --json
anymail whoami --json
anymail feedback create --type bug-report --title "..." --description "..." --json
anymail feedback create --type bug-report --title "..." --description "..." --user-confirmed --json
anymail feedback list --json
anymail feedback status --id <id> --json
anymail feedback inbox --json
anymail feedback read --update-id <id> --json
```

Rules:

- Never put the Portal `jz_` on argv, in chat, or in feedback title/description.
- First run `feedback create` without `--user-confirmed`. The CLI prints the exact draft plus the automatic diagnostic context. Show that preview to the human.
- Only pass `--user-confirmed` after the human approved that exact draft. Do not submit on the first call.
- Title and description must not contain mailbox addresses.
- Prefer recoverable product actions first; use Tell-Jacky for product gaps, not mail content dumps.
- Offline / unknown audience falls back to a local mirror under the platform home; still never invent an admin plugin.
