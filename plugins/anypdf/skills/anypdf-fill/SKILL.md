---
name: anypdf-fill
description: Fill a registered PDF form through the AnyPDF server-owned workflow.
---

# Registered PDF fill

Use the packaged `anypdf` text launcher. First use `connect-anypdf` to save the
credential locally; normal commands then need no environment setup. Never ask
the user to place a token in an argument or echo a token. An explicit
`ANYPDF_TOKEN` environment override is supported only for automation.

1. Resolve the user's form request and show all candidates when there is no
   single clear match:

   ```bash
   anypdf forms resolve --query "IMM 5257"
   anypdf forms catalog
   ```

   Do not guess a form or version. Optional `anypdf preferences` facets are a
   soft ranking hint only and never authorize access.

2. Fetch the chosen schema and build data matching its `schema_version`:

   ```bash
   anypdf forms schema --form-id IMM5257 --version <version>
   anypdf validate --form-id IMM5257 --version <version> --input /absolute/data.json
   ```

   Extraction rules (server-owned; do not invent a private procedure):
   - Read `agent_extraction_contract` and, when present, top-level
     `schema["x-anypdf-guidance"]` (labels, source hints, cross-field rules).
   - Read the sibling `user_knowledge` block: this user's own saved filling
     requirements for this form. They are **advisory** — where one disagrees
     with the schema or `x-anypdf-guidance`, the server-owned side wins.
     Tell the user which of them you applied this time, e.g. "applied 2 of your
     saved requirements: ...". Take the count from the response, never assume it.
   - Extract only facts supported by the user's materials. Align keys exactly
     to the schema. Preserve enum labels and boolean values exactly.
   - Date format follows each field's own `format` / guidance notes — do not
     force a global date pattern when the field disagrees.
   - Never invent missing required facts. Ask the user. Leave unknown optional
     fields absent.
   - Validation errors stop submission. Warnings require explicit user
     confirmation before proceeding (confirm each warning with the user).
   - MUST surface each validate `infos[]` item to the user (path+message); never invent data to clear them.

3. Submit exactly one idempotent job. Keep the same key when retrying a request:

   ```bash
   anypdf fill submit --form-id IMM5257 --version <version> \
     --schema-version <schema_version> --input /absolute/data.json \
     --idempotency-key <stable-key>
   ```

   The response contains `job_id`, `status_url`, and `result_url`. It contains
   no template, mapping, profile, or PDF bytes.

4. Poll manually. Each invocation performs one GET; wait according to
   `Retry-After` and invoke status again. Do not implement a long-held poll or a
   business completion timeout:

   ```bash
   anypdf fill status --job-id <job_id>
   ```

   On `succeeded`, download to a new explicit absolute path:

   ```bash
   anypdf fill download --job-id <job_id> --output /absolute/result.pdf
   ```

   The client bounds the download, verifies `%PDF-` and any server checksum,
   and writes atomically. It refuses symlinks and existing output paths.

## Remembering how this user wants forms filled

When the user states a standing preference about **how** to fill a form —
"employer name always in full legal form", "I give you dates as YYYY-MM-DD",
"only fill part A" — save it and tell them you did:

```bash
anypdf knowledge add --form-id IMM5257 --rule "employer name in full legal form"
anypdf knowledge list --form-id IMM5257
anypdf knowledge remove --id <rule-id>
```

- Record **how to fill**, never **what to fill**. "Use the registered company
  name" is a rule; "the employer is Acme Ltd" is the user's data and does not
  belong here.
- Say so in the moment: "Saved — I'll fill this form that way from now on."
  A requirement the user cannot see is one they can never correct.
- Replacing an earlier requirement is explicit: pass `--supersedes-id <old>`.
  Two rules that contradict each other will both be applied otherwise.
- Use `--origin agent_proposed` only for a rule you proposed and the user
  confirmed; `user_authored` is for what they asked for directly.

Never upload source PDFs, identity documents, or filled evidence as part of a
registered fill. Keep stdout JSON intact and report typed stderr errors.
