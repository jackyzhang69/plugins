---
name: anypdf-fill
description: Fill a registered PDF form through the AnyPDF server-owned workflow.
---

# Registered PDF fill

Use the packaged `anypdf` text launcher. Credentials come only from
`ANYPDF_BACKEND_URL` and `ANYPDF_TOKEN`; never ask for or echo a token.

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

   Validation errors stop submission. Warnings require explicit user
   confirmation before proceeding.

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

Never upload source PDFs, identity documents, or filled evidence as part of a
registered fill. Keep stdout JSON intact and report typed stderr errors.
