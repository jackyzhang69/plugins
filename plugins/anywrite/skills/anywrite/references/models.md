# What a writing method contains

A writing method is one structured record with a title and six parts. It describes how to write, not what to say.

| Part | What it keeps | What it must never contain |
|---|---|---|
| Scope | Field, document type, situation tags, language, who writes, who speaks inside the text, readers, purposes, jurisdiction, style variant | Fixed industry assumptions; a past client's identity |
| Blueprint | The input slots a new document needs; conditions for optional sections; ordered sections, each with its purpose and moves | Facts or conclusions from old cases |
| Expression | Voice, sentence habits, preferred and avoided phrases, a few invented or user-approved anonymised examples | Long passages from originals; private details |
| Presentation | Headings, paragraphs, numbering, citation style, dates, signature and sign-off, format preferences | Anything executable |
| Constraints | What to do when material is missing, where facts may come from, how to check citations, the user's own must-not-break rules | Treating old letters' citations or policies as current law |
| Learning | How many examples, how many independent ones, anonymous labels, what was observed for and against each rule, what the user said directly | Example text, file names or paths, client names, credentials |

Ways to think about it:

- The kind of document is described on separate dimensions (document type, situation tags, readers, language), not one tree. A reply letter and a submission letter differ in document type; the situation is a separate tag.
- The writer and the speaker inside the text are different people when someone writes on another's behalf. Keep them apart.
- A language is not interchangeable with another: do not assume habits carry over between Chinese and English.
- "Open with who is sponsoring, their income source and available funds" is a rule about organisation and belongs in the method. "The father earns X" is a fact about one case and never belongs.
- A "requirement" in a method means the user's own firm writing rule. It is not a statement that the law requires it.
- A condition is either read from an explicit yes/no input in the materials, or judged by the agent from the materials and noted locally. Each result is true, false or unknown; unknown is never silently false.
- Counting evidence: several exports or versions of one document count once. "Seen in 8 of 10" describes the examples seen, not an accuracy. Examples that do not meet a rule's condition do not count in that rule's denominator. A rule the user states directly is recorded as stated by the user, not as observed. One example may teach, with a note that evidence is limited.
- Tone is a set of evidenced expression choices, not a score or a personality label. [Tone](tone.md) maps stable voice, passage-specific variation, same-fact examples and counterevidence into these existing parts; no new schema fields are needed. `when` controls contextual rules, `basis_ids` points to observations, and uncertainty remains visible rather than becoming a mandatory preference.
- Sections are logical parent groups, not a flat catalogue of rhetorical functions. Preserve subtopics through `purpose`, related `moves[*].instruction` actions and section-local `rules`; presentation rules control printed headings, subheadings or no heading. `heading` remains a required nonempty logical label. Do not add nested section fields or `when` to moves: moves have no such field. Use conditional section rules for local applicability and let the move instruction explain how to follow them. Conditions must read new-document inputs, with unknown handled explicitly.
- Use `value_type=text` with `cardinality=many` when a task needs several subtopics or evidence descriptions; `reference` slots can identify supplied records. Define what each slot supplies and which moves use it, rather than inventing object-valued inputs or embedding sample facts. Keep condition evidence available independently of the condition it decides, to avoid self-locking required inputs.
- Observed variation and limits belong in observation descriptions and assessments; unsupported dimensions need no rule. User-stated instructions use `confirmed_instruction` / `user_stated`, zero counts and empty sample references. Proposed safe transformations must be labeled as proposals in review notes and any encoded instruction, never disguised as observed or user-confirmed voice. Actual calibration records what the user chose or said; a model's preference supplies neither authorship evidence nor user consent.
- Multiple observations can support one executable rule via `basis_ids`. Merge overlapping instructions while preserving distinct triggers, placement and boundaries; a repeated theme is not an instruction to summarize every point. A useful ledger observation left only in `learning` has not necessarily become an executable writing instruction: check the path through the exact JSON into a new draft.

The service checks the shape and the cross-references (every referenced input, condition, observation and sample must exist, counts must add up) and flags obvious secrets, absolute paths and email addresses. That is a guard, not proof of anonymity or expression fidelity: the user must read the method before it is saved, and the exact locally validated JSON needs a fresh-session transfer check as in [teach](teach.md).
