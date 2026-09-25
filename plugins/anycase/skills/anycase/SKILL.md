---
name: anycase
description: >-
  READ THIS FIRST for AnyCase. Canadian immigration intelligence: case law
  (FC, FCA, SCC, IRB), IRCC Program Delivery Instructions, Provincial Nominee Programs
  (PNP, BCPNP), LMIA, Help Centre Q&As, practitioner field notes, private confirmed knowledge,
  CLB conversion (IELTS, CELPIP, PTE, TEF, TCF). Connect / login,
  tell Jacky, connect with Jacky / pair session / join code from Jacky.
  Live coverage. One discovery file. Ask `anycase commands --json`,
  never from memory of an older release.
when_to_use: |-
  Load on plugin start. Federal Court, case law, IRCC policy, Help Centre,
  Program Delivery Instructions, coverage, field notes, private knowledge,
  my notes, personal notes, CLB, IELTS General,
  CELPIP-G, PTE Core, TEF Canada, TCF Canada, Provincial Nominee Program,
  PNP, BCPNP, LMIA, connect / log in, tell Jacky,
  connect with Jacky, pair session, join code from Jacky.
  Fill IMM5257 is not this product.
---

# AnyCase — Canadian immigration intelligence

Load this on plugin start and whenever the user asks about Canadian
immigration law, IRCC policy, operational manuals, Provincial Nominee
Programs (PNP, BCPNP), LMIA streams, practitioner notes, private confirmed knowledge, CLB
conversion, live coverage, connect, or Tell Jacky.

## Talk to the human

On the first session after install, read [get-started](references/get-started.md)
before the first user-visible reply. Brief the human in plain language — what
AnyCase does and what to try first — not a command list. After a version bump,
read [whats-new](references/whats-new.md), tell the human what changed in one
breath, then resume their original intent without asking them to repeat the
request. Never ask the human to run doctor as homework. Run the intended
ordinary command first; when stdout is `jz.plugin.envelope.v1`, follow its
exact `status` and `continue_args` until the product reports `ready`, then
resume the sealed request. If `status` is `blocked`, say `say_to_user` to the
human — they have used their lookups — and stop. Do not retry the search, and
do not offer Tell Jacky for the limit.

Speak clearly for consultants and applicants. Report legal conclusions and how
they apply — not raw CLI steps, JSON fields, or backend volumes. Deliver
operational insights as cautious professional workflow guidance; do not present
practitioner observations as official rules or verified consensus.
If users ask about data provenance, point them to official IRCC Program Delivery
Instructions, Help Centre Q&As, and Federal Court judgments — not internal
identities. Never output tokens, internal URLs, or raw system paths.

## When the user asks "what can you do?"

Do not answer from a frozen capability list. Run the live client and translate
`commands --json` into product language:

```bash
"$ANYCASE_BIN" commands --json
```

For live corpus scope, also run `anycase coverage` when the question is about
what is held now — never from memory or a bundled catalog.

## Agent router — intents

| User intent | Host does | Human may be asked |
|---|---|---|
| "what can AnyCase do" / live coverage | Live `"$ANYCASE_BIN" commands --json`; `coverage` when scope matters | Connect once if not logged in ([connect](references/connect.md)) |
| Federal Court / case law / precedent for an issue | Write [questions](references/questions.md) for this question, then [caselaw](references/caselaw.md) | Clarify facts, court scope, or date range when ambiguous |
| IRCC policy / Help Centre Q&A | Write [questions](references/questions.md), then [policy](references/policy.md) | Language preference when both EN/ZH matter |
| Program Delivery Instructions / operational manual | Write [questions](references/questions.md), then [manual](references/manual.md) | Policy code only when the user supplied it |
| Provincial Nominee Programs / BCPNP / OINP / PNP streams / points grids | Write [questions](references/questions.md), then [pnp](references/pnp.md) | Province or stream when not specified |
| Practitioner field notes / practical workflow | Write [questions](references/questions.md), then [notes](references/notes.md) | Topic refinement when the query is too broad |
| Private / personal confirmed knowledge, "my notes", what I saved | Write [questions](references/questions.md), then [knowledge](references/knowledge.md) | Whether they mean their own notes or industry field notes |
| CLB / IELTS General / CELPIP-G / PTE Core / TEF Canada / TCF Canada | [clb](references/clb.md): deterministic CLB conversion | Exact subscores when not provided |
| Program eligibility pre-screen / BC PNP / LMIA / eligibility gating | `check-eligibility`: evaluate client facts against declarative rulesets | Profile JSON path or ruleset |
| Tell Jacky / feedback | [tell-jacky](references/tell-jacky.md) | Confirm the exact draft before send |
| connect with Jacky / pair session / join code from Jacky | [pair-session](references/pair-session.md) | Confirm once that Jacky's assistant may look at this machine's AnyCase status |

Playbooks: [connect](references/connect.md), [questions](references/questions.md), [caselaw](references/caselaw.md),
[policy](references/policy.md), [manual](references/manual.md), [pnp](references/pnp.md),
[notes](references/notes.md), [knowledge](references/knowledge.md), [clb](references/clb.md),
[tell-jacky](references/tell-jacky.md),
[pair-session](references/pair-session.md).

Capability detail (not for first-session orientation):
[references/capabilities.md](references/capabilities.md).

## Live CLI discovery (fail-closed)

```bash
"$ANYCASE_BIN" commands --json
```

Command path reference:
[references/command-surface.md](references/command-surface.md).

AnyCase provides deterministic access to official Canadian immigration
knowledge, statutory calculators, and Canadian immigration case law. It does not
fill PDF forms — that is AnyPDF.
