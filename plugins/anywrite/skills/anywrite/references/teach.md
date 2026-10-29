# Teach: learn a writing method from examples

Use when the user says things like "learn how I write this kind of letter", points to finished documents, or wants to change a saved method.

## 1. Find out what you are teaching

From the examples and the request, work out the kind of document: field, document type, situation, language, who writes, who is speaking inside the text, who reads it, its purpose, jurisdiction if it matters, and any deliberate style variant. Writer and in-text speaker are different things (an adviser may write on behalf of an employer). If the examples mix several document types, come from unknown authors, are mostly quotation, or could not be read properly, say so and ask which to use. Do not treat anything you did not observe as the user's preference.

## 2. Look for an existing method first

Write the known facets as a small JSON object and pipe it in:

`anywrite definitions search --scope-file -` (or `--query <words>`; add `--include-archived` to also see archived ones)

Read the `result` in the answer:

- `exact_match`: a saved method already covers this. Reuse it. Only go on to revise if the user wants to add something new.
- `archived_match`: an archived method has this exact scope. Offer to restore it instead of making a copy.
- `candidates`: open the few that look close with `anywrite definitions show --id <id>` and decide: reuse, revise (same scope, new confirmed rule), variant (same kind but a different reader, language, purpose or style: a new method), or create. If `has_more` is true, say there are more.
- `not_found`: nothing is saved for this yet; create a new one.
- `needs_scope`: you gave neither facets nor words; add them.

If the search returns an error of any kind, the answer is unknown. Never continue as if nothing exists. Say you could not check, retry once, and otherwise stop and explain in plain words.

## 3. Learn from the examples

Read every example yourself. Compare structure (ordered sections and what each is for), wording and tone, layout, how evidence and quotations are handled, and special cases. Count honestly: five exports of one document are one example. A habit seen in 8 of 10 is "seen in 8 of 10 examples", not a rule with an accuracy. One example can teach, but tell the user the evidence is thin. Record what is observed separately from what the user told you directly. See [models](models.md) for the parts of a method.

Follow [tone](tone.md) in order: separate voices, compare like functions and parent/subtopic/move relationships, record evidence and limits, encode conditional rules, consolidate overlap, then check the exact method through new-task transfer and actual user calibration. Keep stable voice and conditional variation distinct; do not substitute generic labels or assume the author is always restrained. Preserve deliberate evaluations, connected reasoning and functional restatement when evidenced. Keep proposed improvements separate from observed voice. Do not claim holdout validation after using every example to derive it.

Build the whole method as one JSON file in a private working location. Use anonymous labels for examples (sample-1, sample-2); keep the mapping to real files only on the user's side. Leave out every client fact, name, amount, quotation and identifier.

## 4. Check locally

`anywrite definitions validate --file <draft>`

This never contacts the service. If `valid` is false, fix every finding (each has a code and a position) and validate again. If valid, keep the `content_hash` it prints: it identifies exactly this draft. Re-read the draft yourself for anything personal the automatic guard cannot see (names, places, numbers that identify a case).

Validation checks the contract and semantic references, not style fidelity. Before asking to save, give this exact JSON to a fresh generator/session together with only the writing references and fully specified invented new-task facts. Do not give it the samples, extraction ledger, reviewer expectations or a separate prose summary of the method. Use the [tone](tone.md) transfer cases and trace evidence ledger → schema fields/IDs → actual draft; check both lost traits and overexecuted traits. Fix the responsible method encoding or execution instruction, validate any changed JSON again and repeat the affected transfer check. Record which hash was exercised. If a fresh session or validator is unavailable, report that check as pending; a same-context draft or model opinion is not a substitute or a passed check.

## 5. Show it and get a yes

Show the user, in plain language and not as JSON: the kind of document it covers, section/subtopic relationships and what each does, when sections apply, tone and wording preferences, layout rules, what happens when material is missing, and how much evidence stands behind each rule. Include the checked draft's `content_hash` and which validation/transfer checks actually ran or remain pending. For tone, show the stable choices, contextual changes, uncertain or conflicting observations, proposed safe transformations, and a short invented contrast that preserves the same facts; ask whether the fitting version sounds like them. Record their actual choice and stated reason separately from model judgments; do not infer a reason or override their preference when votes differ. A response about tone is calibration, not permission to save. Incorporating it changes the candidate: revalidate and repeat the affected transfer check before exact-method confirmation. For a revision, show what is added, changed and removed against the saved method. Say plainly that the method is stored privately in their account and that example texts are not uploaded.

Ask for an explicit yes to saving this exact method (or this exact change). Their yes belongs to the draft whose `content_hash` you showed. If you change anything afterwards, validate again, show the change, and ask again.

## 6. Save

New method:
`anywrite definitions save --file <draft> --confirmed --confirm-hash <content_hash>`

Revision of an existing one (use the `active_revision` you read; the service refuses if someone changed it meanwhile):
`anywrite definitions replace --id <id> --expected-revision <n> --file <draft> --confirmed --confirm-hash <content_hash>`

A new revision keeps the old ones. A different scope needs a new method, not a replacement.

Say it is saved only when the answer has `status` `saved`. Then read it back with `anywrite definitions show --id <id>` and check `integrity.content_hash_verified` is true and the hash equals the one you confirmed. Tell the user the title, the revision, and what is now stored.

## 7. When the answer is not "saved"

- `operation_outcome_unknown`: the network or service dropped before the result was known. The save may or may not have happened. Do not edit the draft and do not make a new one. Run the very same command again: it first asks the service what happened to the earlier attempt and never creates a second copy. If it still says unknown after a retry or two, tell the user and stop; do not say it was saved. To look at what the service recorded for an earlier attempt without repeating it, use `anywrite operations show --key <key>` with the key from the error details.
- `operation_not_applied`: the earlier attempt did not go through; run the same command again. (After a deletion whose answer was lost, the command checks whether the method is already gone and reports the deletion as done.)
- `definition_exists`: a method for this scope is already saved (its id is in the details). Open it and decide between reuse and revise.
- `revision_conflict`: it changed since you read it. Read it again, redo the comparison, show the user, and ask again.
- `scope_changed`: a revision cannot change the scope. Offer a new method instead.
- `confirmation_stale` or `confirmation_mismatch`: the draft and the confirmation do not match. Validate, show, and confirm again.
- `definition_invalid`: the details list findings; fix and validate again.
- Anything about sign-in, access or the service: see [connect](connect.md).

## Other changes

- Archive (hides a method from searches, reversible): `anywrite definitions archive --id <id> --expected-revision <n> --confirmed`
- Restore: `anywrite definitions restore --id <id> --expected-revision <n> --confirmed`
- Export everything about one method to a private file: `anywrite definitions export --id <id> --output <new file>`
- Delete forever, including every revision: `anywrite definitions forget --id <id> --expected-revision <n> --confirm-id <id> --confirmed`

Ask for a clear yes for each, naming the method by its title. For deletion, say it cannot be undone and offer an export first.
