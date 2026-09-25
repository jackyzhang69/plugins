---
name: anycase
description: >-
  READ THIS FIRST for AnyCase. Canadian immigration intelligence: case law
  (FC, FCA, SCC, IRB), IRCC Program Delivery Instructions, Provincial Nominee Programs
  (PNP, BCPNP, NSNP), LMIA, Help Centre Q&As, practitioner field notes, private confirmed knowledge,
  CLB conversion (IELTS, CELPIP, PTE, TEF, TCF). Connect / login,
  tell Jacky, connect with Jacky / pair session / join code from Jacky.
  Live coverage. One discovery file. Ask `anycase commands --json`,
  never from memory of an older release.
when_to_use: |-
  Load on plugin start. Federal Court, case law, IRCC policy, Help Centre,
  Program Delivery Instructions, coverage, field notes, private knowledge,
  my notes, personal notes, CLB, IELTS General,
  CELPIP-G, PTE Core, TEF Canada, TCF Canada, Provincial Nominee Program,
  PNP, BCPNP, NSNP, LMIA, connect / log in, tell Jacky,
  connect with Jacky, pair session, join code from Jacky.
  Fill IMM5257 is not this product.
---

# AnyCase — Canadian immigration intelligence

Load this on plugin start and whenever the user asks about Canadian
immigration law, IRCC policy, operational manuals, Provincial Nominee
Programs (PNP, BCPNP, NSNP), LMIA streams, practitioner notes, private confirmed knowledge, CLB
conversion, live coverage, connect, or Tell Jacky.

## Talk to the human

When the user asks a specific task, directly execute the intended command; do not interrupt with setup narration or bare invocation menus.
Only when invoked without a specific task (bare `/anycase`, bare binary, or asking "what is AnyCase / what can you do"): brief the human in plain language (concise positioning ≤ 5 lines) and append standard options `a` and `b`.
On the first session after install with no specific task, read [get-started](references/get-started.md) and brief the human similarly. After a version bump, read [whats-new](references/whats-new.md), tell the human what changed in one breath, then resume their original intent without asking them to repeat the request. Never ask the human to run doctor as homework. Run the intended
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

## Bare invocation and first interaction (LOCKED)

When the human calls `/anycase` without a specific task, runs the bare CLI, or asks "what is AnyCase / what can you do":
1. Directly present the product's concise positioning and primary capabilities (≤ 5 lines):
   - 权威法律与政策检索（IRCC 政策、审理指南 PDI、联邦法院判例、省提名指南）
   - 法定语言换算（CLB 换算器，支持 IELTS/CELPIP/PTE/TEF/TCF）
   - 确定性项目准入预审（各省提名与 LMIA 项目的法定硬性条件核查）
2. Always append the two standard follow-up options:
   - **输入 a**：如果需要了解本插件详细的知识覆盖范围与完整能力清单，请输入 a。
   - **输入 b**：如果需要了解本插件对您所在行业和职务的意义与价值，请输入 b。
3. Follow-up routing:
   - **User inputs `a`**: Do NOT dump raw legal documents or recite hardcoded numbers. Run `"$ANYCASE_BIN" commands --json`, `"$ANYCASE_BIN" coverage`, and `"$ANYCASE_BIN" programs list --json` to synthesize and present the live knowledge coverage map based strictly on what the live binary returns (the currently held corpora and their index currency/gaps, plus the currently available immigration program pre-screens).
   - **User inputs `b`**: First verify live capabilities via `"$ANYCASE_BIN" commands --json`, then analyze how AnyCase empowers the user's daily workflow, risk control, and decision-making according to their role (e.g. licensed immigration consultant RCIC -> court precedents & PDI policy defence; corporate HR / employer -> LMIA & PNP wage/employer pre-screen; applicant / student -> CLB calculator & provincial streams). If role is unknown, briefly inquire before giving tailored advice.

## Agent router — intents

| User intent | Host does | Human may be asked |
|---|---|---|
| Bare invocation / "what can you do" | Concise positioning + standard prompt options a/b | None |
| User inputs "a" (Knowledge coverage & capability map) | Live `"$ANYCASE_BIN" commands --json`, `coverage`, and `programs list --json` | Scope refinement if needed |
| User inputs "b" (Industry & role value analysis) | Analyze practical ROI/workflow value based on user's role | Ask industry/role if unknown |
| Federal Court / case law / precedent for an issue | Write [questions](references/questions.md) for this question, then [caselaw](references/caselaw.md) | Clarify facts, court scope, or date range when ambiguous |
| IRCC policy / Help Centre Q&A | Write [questions](references/questions.md), then [policy](references/policy.md) | Language preference when both EN/ZH matter |
| Program Delivery Instructions / operational manual | Write [questions](references/questions.md), then [manual](references/manual.md) | Policy code only when the user supplied it |
| Provincial Nominee Programs / BCPNP / OINP / PNP streams / points grids | Write [questions](references/questions.md), then [pnp](references/pnp.md) | Province or stream when not specified |
| Practitioner field notes / practical workflow | Write [questions](references/questions.md), then [notes](references/notes.md) | Topic refinement when the query is too broad |
| Private / personal confirmed knowledge, "my notes", what I saved | Write [questions](references/questions.md), then [knowledge](references/knowledge.md) | Whether they mean their own notes or industry field notes |
| CLB / IELTS General / CELPIP-G / PTE Core / TEF Canada / TCF Canada | [clb](references/clb.md): deterministic CLB conversion | Exact subscores when not provided |
| Discover / list immigration programs & requirements | `programs list` / `programs describe <id>`: inspect supported streams and required fields | Jurisdiction (e.g. CA-BC, CA-FED, CA-NS, FED) |
| Program eligibility pre-screen / BC PNP / LMIA / NSNP / PNP streams | `check-eligibility --program <id> --profile <path>`: evaluate client facts against declarative rulesets | Profile JSON path and target program ID |
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
