# Policy-grid scoring (AnyCase)

Scores an applicant's facts against a published points grid (for example a
provincial or federal selection grid). The grids and their rules come from the
signed-in AnyCase service; the arithmetic runs on this device, and the
applicant's inputs are never sent anywhere. A score compares an applicant with
a grid. It is not an eligibility finding and not a success rate.

## Order of work

1. `anycase score policies --json` lists the published grids: id, name, rule
   version, revision, verification state and effective date. `--changed-since <revision or date>`
   keeps only grids that changed. Needs sign-in; if the envelope says the user
   is not connected, follow [connect](connect.md).
2. `anycase score inputs <policy-id> --json` prints the fields that grid needs.
   Fill them from the user's own documents. Do not invent a field and do not
   guess a missing answer.
3. Write the answers to a local JSON file, then
   `anycase score evaluate --policy <policy-id> --inputs <file> [--as-of <YYYY-MM-DD>] --json`.
   The result is the score breakdown and coverage. `--as-of` picks the grid
   revision that was in force on that day.
4. If facts are missing, evaluation stops with a `missing_inputs` result that
   lists them and a resume token. Ask the user for those facts, add them to the
   same file, and run again with `--resume <token>`. Pinning an exact revision
   with `--revision <n> --payload-sha256 <hash>` cannot be combined with
   `--resume`.
5. `anycase score rank --inputs <file> --json` evaluates every grid that the
   inputs fully cover and lists them by score. Say plainly that the order is a
   comparison of grid scores, not a ranking of chances.
6. `anycase score whatif --policy <policy-id> --inputs <file> --changes <file> --json`
   re-scores after changes. The changes file is a JSON list of
   `{"field": ..., "value": ...}` objects.
7. `anycase score diff <old-result> <new-result>` compares two result files
   saved earlier from `score evaluate --json`. It works offline and needs no
   sign-in.

## Talking to the human

Give the total and the main components in plain words, name the grid revision
date, and repeat that the score is not an eligibility decision. Never print the
inputs file back to the user unless they ask for it.
