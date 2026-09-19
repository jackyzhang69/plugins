# Questions JSON for a search

Before every search — case law, Help Centre, manuals, practitioner notes, or
private notes — write JSON for **this user question**. Use your own reading of
what they asked. We only give the shape and the job. You fill six fields.

Job: each retrieved passage will be checked against this JSON. Keep a passage
only if it actually answers this question.

Write from the user question only. Do not look at search results first. Do not
reuse a previous file. Do not show this JSON to the user. Output the JSON
object only — no markdown fence, no commentary.

Copy `answers_question`, `"type": "noul"`, `instructions.question`, and
`instructions.focus` **exactly**. Do not translate them. Do not add keys.

Fill only these six fields, in the user's language:

- `true.what` — what a useful hit must actually say for **this** question
- `true.not_for` — a near-miss that looks useful but does **not** answer it
- `true.examples` — one or two short made-up passages that should pass
- `false.what` — related, excluded, or opposite content that must not pass
- `false.not_for` — a near-miss that looks like a reject but **does** answer
- `false.examples` — one or two short made-up passages that should fail

Do not leave any of those strings empty. Do not copy the blank template
wording. Use your intelligence: how-to needs the method; an exclusion makes
that kind false; a contrast or outcome belongs in the user's words. The same
shape covers practitioner notes, recipes, and court passages.

```json
{
  "answers_question": {
    "type": "noul",
    "instructions": {
      "question": "Should this passage be returned as an answer to the user question?",
      "focus": "True only if the passage supplies what the user asked. Same topic without that content, or content the user said not to use, must be false."
    },
    "criteria": {
      "true": {
        "what": "",
        "not_for": "",
        "examples": [""]
      },
      "false": {
        "what": "",
        "not_for": "",
        "examples": [""]
      }
    }
  }
}
```

Pass `--questions <path>` on `caselaw`, `policy`, `manual`, `notes`, or
`knowledge` when the live `commands --json` lists that flag. If it does not,
search without it.
