# Questions JSON for a search

Before every search, add `questions` to the search `--input` JSON. Write it for
**this user question**. Use your own reading of what they asked. We only give
the shape and the job. You fill six fields.

Job: search returns candidates. Request criteria are applied to the complete
note when that check ran. Keep a note only if it actually answers this
question about the same object. A mention of a method is not the method.

Write from the user question only. Do not look at search results first. Do not
reuse JSON from a previous question. Do not show this object to the user.

Fill only these six fields, in the user's language. Do not add keys.

- `true.what` — what a useful hit must actually say for **this** question
- `true.not_for` — a near-miss that looks useful but does **not** answer it
- `true.examples` — one or two short made-up passages that should pass
- `false.what` — related, excluded, or opposite content that must not pass
- `false.not_for` — a near-miss that looks like a reject but **does** answer
- `false.examples` — one or two short made-up passages that should fail

Do not leave any of those strings empty. Do not copy the blank template
wording. Use your intelligence: how-to needs the method; an exclusion makes
that kind false; a contrast or outcome belongs in the user's words.

```json
{
  "query": "the user's search text",
  "limit": 5,
  "questions": {
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
```

If a live search rejects the `questions` field, omit it and search with `query`
(and `limit`) only.
