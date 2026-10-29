# Write: draft a new document from a saved method

Use when the user wants a new document written the way they have taught before.

## 1. Choose and pin the method

Search for the right one as in [teach](teach.md) (`anywrite definitions search`), then read it with `anywrite definitions show --id <id>`. Check `integrity.content_hash_verified` is true. If several fit, show the choices in plain words and let the user pick; if none fit, offer to teach one first. Any search or read error means you do not know yet; never invent a method.

Pin the method for this whole piece of writing: remember its id, revision and content hash. If a newer revision appears while you work, keep using the pinned one unless it was withdrawn.

## 2. Gather this document's materials

Take today's facts only from the materials the user gives you for this document, and from sources they explicitly allow you to check. Keep, for each fact, a local note of where it came from. Fill the method's input slots from these materials. Never take names, dates, amounts, jobs, backgrounds, conclusions or quotations from the examples the method was learned from, from earlier documents, or from the method itself. Laws, policies and cases change: only cite what you verified now from a current source; "an earlier letter said so" is not evidence. If sources contradict each other, keep both and stop asserting the disputed point rather than picking the latest date.

## 3. Decide which sections apply

For each conditional section, judge the condition as true, false or unknown from this document's materials. Unknown is not false. If a missing piece matters, ask the user for it; if they cannot supply it yet, write the section as a clearly marked gap (for example "[to confirm: ...]") and say the draft is not final. Do not turn unknown into a confident statement.

Make a short content map for yourself: which sections will be written, which facts go in each, which sections are skipped and why, which are marked as gaps. Following [tone](tone.md), map each passage's function to the method's stable voice and applicable conditional expression rules. Judge those conditions from today's materials, not from the presence of a similar passage in a sample. If the requested language, reader, speaker or document type is outside the learned scope, flag that boundary and offer adaptation or teaching rather than claiming an unobserved voice was learned.

Keep parent section → subtopic → related moves in that map. A logical section label need not become a printed heading; follow presentation rules rather than giving every fact, explanation or judgment its own heading. Apply local conditional rules within the parent group, including their unknown behavior. Identify which instructions describe the same action so one well-placed passage can satisfy them together; do not multiply recaps to satisfy overlapping wording.

## 4. Write the draft

Follow the method's sections and their moves in order, then its wording, tone, layout, citation, signature and constraint rules. Preserve the learned level of directness, warmth, advocacy and sentence rhythm where supported; do not flatten them into generic professional prose or apply every trait to every paragraph. Use the method's preferred phrases where they fit and avoid the ones it avoids, without phrase stuffing. Where the method gives examples, use them only to understand style, never as evidence or facts. Today's facts and uncertainty take priority over a stylistic effect.

When evidenced by the method, retain explicit supported judgments, cumulative reasoning, reader guidance and restatement that serves a new function at the specified location. Concision alone is not a reason to delete them; for a more understated method, do not add them as generic improvements. Proposed safe transformations are distinct from claims about the author's observed voice. Never reproduce an error, unsupported guarantee or unverified legal claim to make the draft sound authentic.

Deliver editable Markdown or plain text. Only if the host has a real tool for Word or PDF may you also produce that; then actually produce it, open or inspect the result, and report it only if that check passed. If you have no such tool, say that you cannot make that format and give the text version. Never claim a file you did not create and verify.

## 5. Check before handing over

Check that every fact traces to this document's materials, that no sample fact leaked in, that each section follows the method, that missing material is visibly marked, and that wording and layout rules are respected. Apply the sentence-level checks in [tone](tone.md): verify speaker, directness, context-specific intensity and rhythm; reject invented triggers, unsupported evidence pointers, and changes to negation, intention, commitment or certainty. Correct factual drift before expression drift. Tell the user plainly which parts are assumptions or gaps.

Trace the applicable rule/move IDs to actual passages or a reason for non-application. Check that nested reasoning survived and that recurring emphasis has its intended function without mechanically repeated conclusions. A valid method hash does not establish this. During a local teach calibration, use only the supplied exact candidate JSON and new facts in a fresh session (no saved-method lookup or saving); report execution gaps instead of silently reconstructing the method from samples or evaluator hints. A polished draft cannot prove that a trait absent from the encoded method was learned.

A one-off change of wording the user asks for in this draft does not change the saved method. If they want it to become part of how you write this kind of document, treat it as a new teaching step and ask to save it as a revision ([teach](teach.md)); nothing is saved without their yes.
