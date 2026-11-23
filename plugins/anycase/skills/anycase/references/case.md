# Local case files (AnyCase)

A case file is a small JSON record kept in the user's own `Cases` folder
(`~/Cases/<case-id>/case.v2.json`, or the folder named by `ANYCASE_CASES_ROOT`).
It records a program pin, a template and the user's answers to the IMM 5708
task inputs. Everything here is offline, deterministic and local; nothing is
sent to a server and no model is called. A case file holds client facts: never
paste it into feedback or a pair session.

## Order of work

1. Pick the program and template. `anycase programs list` shows catalog ids; a
   template can also use its own program id verbatim (the built-in template
   `visitor-extension` uses `canada.visitor.extension`). Then
   `anycase case init --program <code> --template <name> --case-id <id>`
   opens the case. The id is lowercase letters, digits and dashes, for example
   `case-2026-vr-001`. It refuses to overwrite an existing case.
2. `anycase case show --case-dir <dir>` prints the whole case document. `<dir>`
   is the case folder; the path of `case.v2.json` also works.
3. Record answers one at a time with
   `anycase case update --case-dir <dir> --set <key> <value>`, or merge a JSON
   file of answers with `--intent <file>` (the two cannot be combined). A value
   is the answer, `unknown` when the client does not know, or `none` where the
   form allows a blank. Keys are the IMM 5708 task inputs, such as
   `visit_purpose`, `visit_to_date`, `bg_criminal`, plus `representative`;
   `case plan` lists all of them. An unknown key is refused with the valid list.
4. `anycase case derive --case-dir <dir> --json` expands the template task pack
   and lists the items still unanswered. Ask the user for those, update, and
   derive again until nothing is unresolved.
5. `anycase case plan --case-dir <dir>` prints the declarative task package that
   downstream tools (AnyPDF, AnyWeb, Bridge) consume. This command only emits
   the package; it does not fill any form.

## Optional PostgreSQL case store

`anycase case db ...` repeats the same operations against a PostgreSQL store.
It works only when `ANYCASE_DATABASE_URL` is set; without it these commands fail
and the local files above are the only path.

- `anycase case db migrate` applies the schema (safe to repeat) and
  `anycase case db seed` mirrors the built-in templates and their engine config
  into the database.
- `anycase case db list` lists stored cases; `anycase case db targets --template <name>`
  shows one template's engine targets; `anycase case db show --case-id <id>`
  prints one case.
- `anycase case db init --program <code> --template <name> --case-id <id>`,
  `anycase case db derive --case-id <id>` and `anycase case db plan --case-id <id>`
  mirror `case init`, `case derive` and `case plan`.
- `anycase case db update --case-id <id> --set <key> <value>` mirrors `case update`
  and refuses the write when `--expect-revision <n>` does not match the stored
  revision (it defaults to the stored revision).
- `anycase case db delete --case-id <id>` deletes a case and its children. Ask the
  user first; a missing case is an error.

## Talking to the human

Say what was saved in plain words ("I recorded the purpose of the visit"), list
the open questions in the user's language, and never read intent keys aloud.
