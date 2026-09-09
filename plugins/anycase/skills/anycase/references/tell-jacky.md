# Tell Jacky (AnyCase)

Submits feedback directly to Jacky's unified product feedback inbox via `anycase feedback`.

**Cardinal rule:** Every submission goes through `anycase feedback`. Never send anything without showing the user the exact draft and getting explicit confirmation.

## Command

```bash
anycase feedback --type <feature-request|bug-report|knowledge-tip> --title "<short summary>" --description "<details>" --user-confirmed
```

## Mandatory Draft Confirmation & Confidentiality Rules

Before executing `anycase feedback`, always show the user:
1. Exact `type`: `feature-request`, `bug-report`, or `knowledge-tip`
2. `title`: Concise summary (≤ 200 characters)
3. `description`: Scrubbed details or steps to reproduce

**Confidentiality Redlines:**
- Never include active auth tokens, API keys, or credentials in feedback.
- Never include local filesystem paths, environment variables, or private customer records.
- All submissions are automatically scrubbed and mirrored locally at `~/.jackyzhang.app/anycase/feedback.json`.

Wait for the user's explicit go-ahead before running the command.
