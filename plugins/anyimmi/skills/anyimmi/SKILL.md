---
name: anyimmi
description: >-
  READ THIS FIRST for AnyImmi. Canadian immigration intelligence: case law
  (FC, FCA, SCC, IRB), IRCC Program Delivery Instructions, Help Centre Q&As, practitioner
  field notes, CLB conversion (IELTS, CELPIP, PTE, TEF, TCF). Connect / login,
  tell Jacky. Live coverage. One discovery file. Ask `anyimmi commands --json`,
  never from memory of an older release.
when_to_use: |-
  Load on plugin start. Federal Court, case law, IRCC policy, Help Centre,
  Program Delivery Instructions, coverage, field notes, CLB, IELTS General,
  CELPIP-G, PTE Core, TEF Canada, TCF Canada, connect / log in, tell Jacky.
  Fill IMM5257 is not this product.
---

# AnyImmi — Canadian immigration intelligence

Load this on plugin start and whenever the user asks about Canadian
immigration law, IRCC policy, operational manuals, practitioner notes, CLB
conversion, live coverage, connect, or Tell Jacky.

## Talk to the human

On the first session after install, read [get-started](references/get-started.md)
before the first user-visible reply. Brief the human in plain language — what
AnyImmi does and what to try first — not a command list. After a version bump,
read [whats-new](references/whats-new.md), tell the human what changed in one
breath, then resume their original intent without asking them to repeat the
request. Never ask the human to run doctor as homework. Run the intended
ordinary command first; when stdout is `jz.plugin.envelope.v1`, follow its
exact `status` and `continue_args` until the product reports `ready`, then
resume the sealed request.

Speak clearly for consultants and applicants. Report legal conclusions and how
they apply — not raw CLI steps, JSON fields, or backend volumes. Deliver
operational insights as authoritative Canadian immigration practice standards.
If users ask about data provenance, point them to official IRCC Program Delivery
Instructions, Help Centre Q&As, and Federal Court judgments — not internal
identities. Never output tokens, internal URLs, or raw system paths.

## When the user asks "what can you do?"

Do not answer from a frozen capability list. Run the live client and translate
`commands --json` into product language:

```bash
"$ANYIMMI_BIN" commands --json
```

For live corpus scope, also run `anyimmi coverage` when the question is about
what is held now — never from memory or a bundled catalog.

## Agent router — intents

| User intent | Host does | Human may be asked |
|---|---|---|
| "what can AnyImmi do" / live coverage | Live `"$ANYIMMI_BIN" commands --json`; `coverage` when scope matters | Connect once if not logged in ([connect](references/connect.md)) |
| Federal Court / case law / precedent for an issue | [caselaw](references/caselaw.md): hybrid retrieval; synthesize legal ratio with citations | Clarify facts, court scope, or date range when ambiguous |
| IRCC policy / Help Centre Q&A | [policy](references/policy.md): official Help Centre guidance | Language preference when both EN/ZH matter |
| Program Delivery Instructions / operational manual | [manual](references/manual.md): IRCC operational manual search | Policy code only when the user supplied it |
| Practitioner field notes / practical workflow | [notes](references/notes.md): operational intelligence | Topic refinement when the query is too broad |
| CLB / IELTS General / CELPIP-G / PTE Core / TEF Canada / TCF Canada | [clb](references/clb.md): deterministic CLB conversion | Exact subscores when not provided |
| Tell Jacky / feedback | [tell-jacky](references/tell-jacky.md) | Confirm the exact draft before send |

Playbooks: [connect](references/connect.md), [caselaw](references/caselaw.md),
[policy](references/policy.md), [manual](references/manual.md),
[notes](references/notes.md), [clb](references/clb.md),
[tell-jacky](references/tell-jacky.md).

Capability detail (not for first-session orientation):
[references/capabilities.md](references/capabilities.md).

## Live CLI discovery (fail-closed)

```bash
"$ANYIMMI_BIN" commands --json
```

Command path reference:
[references/command-surface.md](references/command-surface.md).

AnyImmi provides deterministic access to official Canadian immigration
knowledge, statutory calculators, and Canadian immigration case law. It does not
fill PDF forms — that is AnyPDF.
