---
name: anypdf-form-intake
description: Owner-only entrypoint for one or more blank PDFs; identify-only output is exactly anypdf.owner-intake.v1.
---

# AnyPDF owner form intake

## Identity and authority

This is the Jacky-private orchestration skill, not the homonymous public upload-only skill.
Identify-only is a literal protocol response: the entire response content must be exactly the
following single line, preserving lowercase letters and punctuation without translation,
reformatting, a label, Markdown, or commentary:

```text
anypdf.owner-intake.v1
```

Identify-only performs no tool call, file write, network request, or other mutation. If a fresh
host invocation does not select this
contract unambiguously, stop before intake and repair installed-skill precedence. Never rename the
entrypoint to hide a resolution failure.

`docs/admin/new-form-onboarding.md` is the sole lifecycle authority. Read it and the current repo
copy of `admin/skills/anypdf-admin-onboard/SKILL.md` before action. This skill only composes intake,
batch custody, and exact-job onboarding. It does not weaken evidence, invent a second lifecycle,
or move private capabilities into the public plugin.

Before dispatching any workflow, write the current task's explicit **Goal**, **Do**, **Do not**, and
**Termination conditions**. The named PDF set and the user's current-session confirmation that
every file is a blank template are required. A filled form, identity document, source evidence, or
ambiguous file is a hard stop.

## One-or-many batch contract

1. **Identify when requested.** In identify-only mode return only the contract marker above and
   stop. Do not inspect files or environment state.
2. **Preflight the complete named set.** Use the repository-pinned
   `scripts/anypdf_intake_batch.py` helper. Require absolute regular non-symlink files, `%PDF-`, the
   size limit, and the exact blank-template confirmation. Hash every input before the first upload.
   On resume, re-hash and compare with the ledger; content drift is a durable conflict, never a
   resubmit. Collapse identical content hashes deterministically, retain every alias path in the
   private ledger, and submit only the canonical item. The helper-derived per-content idempotency
   key is authoritative; do not invent another key. Select and retain a new absolute private batch
   root for the named set; a released root is immutable and cannot be reused for another batch.
   Invoke the helper through the exact AnyPDF checkout:

   ```text
   uv run python scripts/anypdf_intake_batch.py prepare --root PRIVATE_BATCH_ROOT \
     --owner-run-id OWNER_RUN --confirm --path /absolute/one.pdf [--path /absolute/two.pdf] --json
   ```
3. **Hold single-flight custody.** The private ledger uses an owner run ID, version, epoch, and
   bounded lease. Every mutation must match all four. A competing live owner stops. After expiry,
   takeover requires explicit current-session stale-recovery confirmation, increments the epoch,
   and preserves the prior audit event. Never delete or edit a lock/ledger by hand.
   Pass the returned `version` and `epoch` to every `record`, `pin-release`, and `release`
   helper action; stale context is a hard stop, not a blind retry.
4. **Submit each unique PDF.** Use the governed proactive-intake authority with the public client:

   ```text
   anypdf intake submit --pdf /absolute/blank.pdf --confirm-blank --idempotency-key STABLE_KEY
   ```

   Record the returned `submission_id`, `status`, and `job_id` immediately. If the response is lost
   or remains `awaiting_upload`, re-run the exact same file/key command so the server's existing
   get-or-create/finalize contract performs authoritative reconciliation, then read status by the
   recorded `submission_id`. A different submission or job ID for the same content key is a durable
   conflict and hard stop; never overwrite it.
5. **Interpret every intake status exactly.** `awaiting_upload` is non-terminal only inside the
   idempotent submit/finalize reconciliation above. `queued` binds one exact onboarding job.
   `known_exact` is an existing-form success only after exact public schema and fill readiness are
   proved. Proof-verified `active` resolves to that same `known_exact` success classification.
   `needs_review`, `ready_to_publish`, and `published` are non-success recovery states and continue
   only through authoritative exact-job recovery. `rejected`, `failed`, and `superseded` are
   precise non-success terminal hard stops for that item. Any unknown status is protocol drift and
   stops the batch.
6. **Close exact jobs, never the queue.** For every canonical `queued` item, retain its returned job
   ID and apply the repo copy of `$anypdf-admin-onboard` with `--expected-job-id JOB`. An absent or
   ineligible exact job is no work; never claim another queued item. After any uncertain response,
   read authoritative job/run/revision state and use only the canonical recovery table.
7. **Seal once, deploy at most once, publish serially.** Finish and seal every unique local
   candidate before production publication. Consolidate any shared code changes into at most one
   governed release, then pin the exact 40-character release SHA in the ledger. A SHA change starts
   a new batch epoch and requires re-sealing; per-item deployments are forbidden. Publish in sealed
   order and stop at the first failure. Every `production-complete` item must prove public
   fill/download/allowlist and `/readyz` equal to the pinned SHA.
8. **Close and report precisely.** A unique item succeeds only as `known_exact` (including a
   proof-resolved `active`) or `production-complete`. Duplicate aliases inherit their canonical
   item's result. Record terminal or hard-stop state before releasing custody. Batch success means
   every unique item succeeded; intermediate states are never reported as complete.

## Public/private boundary

The public `anypdf-form-intake` skill remains upload-only. Never copy this private skill, its batch
ledger/helper, admin skill names or commands, tokens, PDFs, mappings, fixtures, receipts, prompts,
or release evidence into a public plugin/package. Intake, queue, forms, publication, and public
automation credentials remain separate and enter only through governed environment injection;
never print them or pass raw values as arguments.

## Termination conditions

Success requires every canonical input to be `known_exact` or `production-complete`, the ledger to
be closed, aliases reconciled, and the precise per-item result reported. Stop immediately on
ambiguous skill resolution or source, live competing custody, hash/ID/SHA conflict, unknown status,
exact-job mismatch, canonical hard boundary, repeated failure signature, lost authority, or secret
exposure risk. Preserve the ledger and authoritative IDs for recovery; never convert a stop into a
success summary.
