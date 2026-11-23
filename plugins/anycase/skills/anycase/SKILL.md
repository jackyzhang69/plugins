---
name: anycase
description: >-
  READ THIS FIRST for AnyCase. Canadian immigration intelligence: case law
  (FC, FCA, SCC, IRB), IRCC Program Delivery Instructions, Provincial Nominee Programs
  (PNP, BCPNP, NSNP), LMIA, Help Centre Q&As, practitioner field notes, private confirmed knowledge,
  CLB conversion (IELTS, CELPIP, PTE, TEF, TCF). Connect / login,
  tell Jacky, connect with Jacky / pair session / join code from Jacky.
  Live coverage. One discovery file; the command list below is the complete
  surface of this version.
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
   - **User inputs `a`**: Do NOT dump raw legal documents or recite hardcoded numbers. Run `"$ANYCASE_BIN" commands --json` (confirms the command list below), `"$ANYCASE_BIN" coverage`, and `"$ANYCASE_BIN" programs list --json` to synthesize and present the live knowledge coverage map based strictly on what the live binary returns (the currently held corpora and their index currency/gaps, plus the currently available immigration program pre-screens).
   - **User inputs `b`**: Use the command list below to see what AnyCase can do, then analyze how AnyCase empowers the user's daily workflow, risk control, and decision-making according to their role (e.g. licensed immigration consultant RCIC -> court precedents & PDI policy defence; corporate HR / employer -> LMIA & PNP wage/employer pre-screen; applicant / student -> CLB calculator & provincial streams). If role is unknown, briefly inquire before giving tailored advice.

## Agent router — intents

| User intent | Host does | Human may be asked |
|---|---|---|
| Bare invocation / "what can you do" | Concise positioning + standard prompt options a/b | None |
| User inputs "a" (Knowledge coverage & capability map) | `"$ANYCASE_BIN" commands --json`, `coverage`, and `programs list --json` | Scope refinement if needed |
| User inputs "b" (Industry & role value analysis) | Analyze practical ROI/workflow value based on user's role | Ask industry/role if unknown |
| Federal Court / case law / precedent for an issue | Write [questions](references/questions.md) for this question, then [caselaw](references/caselaw.md) | Clarify facts, court scope, or date range when ambiguous |
| IRCC policy / Help Centre Q&A | Write [questions](references/questions.md), then [policy](references/policy.md) | Language preference when both EN/ZH matter |
| Program Delivery Instructions / operational manual | Write [questions](references/questions.md), then [manual](references/manual.md) | Policy code only when the user supplied it |
| Provincial Nominee Programs / BCPNP / OINP / PNP streams / points grids | Write [questions](references/questions.md), then [pnp](references/pnp.md) | Province or stream when not specified |
| Practitioner field notes / practical workflow | Write [questions](references/questions.md), then [notes](references/notes.md) | Topic refinement when the query is too broad |
| Private / personal confirmed knowledge, "my notes", what I saved | Write [questions](references/questions.md), then [knowledge](references/knowledge.md) | Whether they mean their own notes or industry field notes |
| CLB / IELTS General / CELPIP-G / PTE Core / TEF Canada / TCF Canada | [clb](references/clb.md): deterministic CLB conversion | Exact subscores when not provided |
| Discover / list immigration programs & requirements | `programs list` / `programs describe <id>`: inspect supported streams and required fields | Jurisdiction (e.g. live discovery via programs list) |
| Read published document-list options for a pathway and stage | `documents list --json`, then `documents resolve --pathway-id <pathway-id> --document-list-id <listed-id> --stage <stage> --channel <channel> --json` (channel defaults to `default`); retain `program_release_id`, `document_list_revision_id`, and `payload_sha256` | Exact pathway, stage, channel, and listed document-list selector |
| Evaluate document-list applicability against local case facts | `documents evaluate --pathway-id <pathway-id> --document-list-id <listed-id> --stage <stage> --channel <channel> --program-release-id <release-id> --revision-id <revision-id> --payload-sha256 <hash> --facts <local-json> --as-of <YYYY-MM-DD> --json` uses all three exact pin flags from the resolve result, then evaluates locally; facts are not sent to ImmiCore | Local fact profile and explicit evaluation date |
| Program eligibility pre-screen / BC PNP / LMIA / NSNP / PNP streams | `check-eligibility --program <id> --profile <path>`: evaluate client facts against declarative rulesets | Profile JSON path and target program ID |
| Points / policy-grid scoring, what-if, compare two score results | [score](references/score.md) | Applicant inputs and evaluation date |
| Open or maintain a local case file (IMM 5708 intent, task plan) | [case](references/case.md) | Case id, program and template; each intent answer |
| Tell Jacky / feedback | [tell-jacky](references/tell-jacky.md) | Confirm the exact draft before send |
| connect with Jacky / pair session / join code from Jacky | [pair-session](references/pair-session.md) | Confirm once that Jacky's assistant may look at this machine's AnyCase status |

Playbooks: [connect](references/connect.md), [questions](references/questions.md), [caselaw](references/caselaw.md),
[policy](references/policy.md), [manual](references/manual.md), [pnp](references/pnp.md),
[notes](references/notes.md), [knowledge](references/knowledge.md), [clb](references/clb.md),
[score](references/score.md), [case](references/case.md),
[tell-jacky](references/tell-jacky.md),
[pair-session](references/pair-session.md).

Capability detail (not for first-session orientation):
[references/capabilities.md](references/capabilities.md).

## Command list (complete for this version)

This list is the complete command surface of this version; `"$ANYCASE_BIN" commands --json` prints the same list and confirms it. Entries omit the `anycase` prefix: run an entry as `"$ANYCASE_BIN" <entry>`, and the command is the words before its first argument or flag. In an entry, `[--flag ...]` in brackets is optional and anything not in brackets is required; `<value>` is a value to fill in, and `"<query>"` is the search text. A flag is named by its long form; a short alias in parentheses, such as `(-l)`, is the same flag, not a separate one. Every command also accepts the optional `[--json]` (output raw JSON). Only coverage, program lists, document lists and scoring grids change between releases; read those live from the CLI.

Account and install:
- `login [--token-stdin]`: save the platform token read from stdin (the token text, never an argument); always pass `--token-stdin`; see [connect](references/connect.md).
- `logout`: remove the saved local credential.
- `whoami`: show masked sign-in status.
- `doctor [--repair-install]`: check install, sign-in and backend; with `--repair-install` it instead makes the package this binary runs from the only installed copy (see Updates).
- `commands`: print this command list as JSON.
- `feedback [--type <feature-request|bug-report|knowledge-tip>] --title "<text>" --description "<text>" --user-confirmed`: send Tell Jacky feedback; `--user-confirmed` is refused unless the user confirmed the draft, so always pass it only after they did; `--type` defaults to `feature-request`; see [tell-jacky](references/tell-jacky.md).

Research, one search per question ([questions](references/questions.md) first). On every `caselaw`, `policy`, `manual`, `pnp`, `notes` and `knowledge` search pass `--questions <file>`, and omit it only if you cannot write the file; `query` and `coverage` take no `--questions`. `[--top <n>]` is optional everywhere, and `[--mode <keyword|semantic|hybrid>]` defaults to `hybrid`:
- `caselaw "<query>" [--court <fc|fca|irb|scc>] [--since <date>] [--until <date>] [--mode <mode>] [--top <n>] [--questions <file>]`: court decisions with legal ratio excerpts.
- `policy "<query>" [--lang <en|zh>] [--mode <mode>] [--top <n>] [--questions <file>]`: IRCC Help Centre Q&As; `--lang` defaults to `en`.
- `manual "<query>" [--policy-code <code>] [--mode <mode>] [--top <n>] [--questions <file>]`: IRCC Program Delivery Instructions; add `--policy-code` only when the user gave one.
- `pnp "<query>" [--province <code>] [--stream <name>] [--mode <mode>] [--top <n>] [--questions <file>]`: Provincial Nominee Program guides and points grids.
- `notes "<query>" [--top <n>] [--questions <file>]`: synthesized practitioner field notes.
- `knowledge "<query>" [--top <n>] [--questions <file>]`: the user's own private confirmed knowledge.
- `coverage`: what the corpora hold right now.
- `query --action <caselaw|policy|notes|knowledge|clb|manual|pnp|coverage> [--input "<query or JSON>"] [--top <n>] [--lang <code>] [--policy-code <code>] [--province <code>] [--stream <name>] [--mode <mode>]`: one structured entry point for the actions above; `--policy-code` applies to manual, `--province` and `--stream` to pnp.
- `clb [--test <ielts|celpip|pte|tef|tcf>] [--listening (-l) <n>] [--reading (-r) <n>] [--writing (-w) <n>] [--speaking (-s) <n>] [--skill <name>] [--score <n>]`: Canadian Language Benchmark levels. Give all four skill scores, or one `--skill` with its `--score`; `--test` defaults to `ielts`; see [clb](references/clb.md).

Programs, eligibility and documents:
- `programs list [--jurisdiction <code>] [--category <word>] [--format <summary|json>]`: supported programs.
- `programs describe <catalog-id> [--format <summary|json>]`: one program's requirements and metadata.
- `check-eligibility --profile <file or -> [--program <id>] [--rules <file>] [--policy-revision <id>] [--format <summary|json>]`: three-state eligibility pre-screen of client facts. The profile is JSON, `-` reads it from stdin; name the program with `--program`, or give a local ruleset with `--rules` instead; `--policy-revision` pins a revision.
- `documents list`: published document lists per pathway.
- `documents resolve --pathway-id <id> --document-list-id <id> [--stage <stage>] [--channel <channel>] [--program-release-id <id> --revision-id <id> --payload-sha256 <hash>]`: the exact active document-list pin; `--stage` defaults to `eapr` and `--channel` to `default`; the three pin flags go together.
- `documents evaluate --pathway-id <id> --document-list-id <id> [--stage <stage>] [--channel <channel>] [--program-release-id <id> --revision-id <id> --payload-sha256 <hash>] [--local-payload <file>] --facts <file> --as-of <date> [--jurisdiction <code>] [--locale <locale>]`: evaluate a list against local facts; `--stage` and `--channel` default as for `documents resolve`; give the three pin flags from `documents resolve`, or `--local-payload` for an unpublished list, not both.

Scoring ([score](references/score.md)):
- `score policies [--changed-since <revision or date>]`: list scoring grids.
- `score inputs <policy-id>`: the fields a grid needs.
- `score evaluate --policy <id> --inputs <file> [--as-of <date>] [--resume <token>] [--revision <n> --payload-sha256 <hash>]`: score one grid; `--resume` continues an earlier incomplete evaluation, the two pin flags go together and cannot be combined with `--resume`.
- `score rank --inputs <file> [--as-of <date>]`: score every full-coverage grid and rank them.
- `score whatif --policy <id> --inputs <file> --changes <file> [--as-of <date>]`: re-score after changing fields.
- `score diff <old-result> <new-result>`: compare two saved results, offline.

Local case files ([case](references/case.md)):
- `case init --program <code> --template <name> --case-id <id>`: open a case folder under the user's Cases folder.
- `case show --case-dir <dir>`: print the case document.
- `case update --case-dir <dir> [--set <key> <value>] [--intent <file>]`: record one answer with `--set`, or merge many from a file with `--intent`; give one of the two, not both.
- `case derive --case-dir <dir>`: expand the task pack and list unanswered items.
- `case plan --case-dir <dir>`: emit the task package for downstream tools.

Optional PostgreSQL case store, the same operations as the local case files; available only when `ANYCASE_DATABASE_URL` is set:
- `case db migrate`: apply the schema (safe to repeat).
- `case db seed`: mirror the built-in templates and their engine config into the database.
- `case db list`: list stored cases.
- `case db targets --template <name>`: one template's engine targets.
- `case db show --case-id <id>`: print one stored case.
- `case db init --program <code> --template <name> --case-id <id>`: open a stored case.
- `case db derive --case-id <id>`: expand the task pack and list unanswered items.
- `case db plan --case-id <id>`: emit the task package.
- `case db update --case-id <id> [--set <key> <value>] [--intent <file>] [--expect-revision <n>]`: record answers as in `case update`; `--expect-revision` refuses the write when it differs from the stored revision (default: the stored revision).
- `case db delete --case-id <id>`: delete a stored case and its children.

Pair session ([pair-session](references/pair-session.md)):
- `pair join --code <code> --user-confirmed`: join Jacky's assistant; refused unless the user agreed, so pass `--user-confirmed` only after they did.
- `pair status`: whether a connection is open.
- `pair inbox`: unread instructions from the assistant.
- `pair read --message-id <id>`: mark one instruction read.
- `pair snapshot --envelope-json <file or ->`: send a status envelope.
- `pair result [--ok] [--error <text>] [--envelope-json <file or ->]`: report the outcome of a requested action: `--ok` for success or `--error <text>` for failure; `--envelope-json` attaches the product envelope.
- `pair close [--reason <text>]`: end the session; `--reason` defaults to `done`.

AnyCase provides deterministic access to official Canadian immigration
knowledge, statutory calculators, and Canadian immigration case law. It does not
fill PDF forms — that is AnyPDF.

## Updates

`anycase` may print one stderr line with schema `jz.plugin.update_notice.v1`. It never blocks a command. Finish what the user asked first, then tell them once, in plain words, that a newer version is available and ask whether to update now. On yes: refresh this plugin through the host (Claude Code: `claude plugin marketplace update jacky-plugins`, then `claude plugin update anycase@jacky-plugins`; Codex: `codex plugin marketplace upgrade jacky-plugins`, then `codex plugin add anycase@jacky-plugins`; other hosts: attach the new `plugins/anycase/` tree). Then run `doctor --repair-install` with the binary from the **new** package, not the folder this session started from (Claude Code: `~/.claude/plugins/cache/jacky-plugins/anycase/<latest>/bin/<platform>/anycase`; Codex: `~/.codex/plugins/cache/jacky-plugins/anycase/<latest>/bin/<platform>/anycase`; `<latest>` is the notice's `latest` value or the highest version folder present; `<platform>` is `darwin-arm64`, `linux-x64` or `win32-x64`). Read `references/whats-new.md` from that folder and tell the user in one or two sentences what changed and that the host loads it fully after a restart. For the rest of this session use that folder for every command and reference; the old version folder is gone. On no: do not ask again in this session.
