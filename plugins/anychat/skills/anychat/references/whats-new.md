# What's new in 0.1.92

**Windows sign-in keeps the supported chat app:** after the supported version is installed, AnyChat pauses automatic replacement before the first open, so signing in does not swap in an unsupported newer build.

# What's new in 0.1.91

**Clearer Windows setup waits:** opening the local archive returns a short in-progress next step within about 15 seconds instead of sitting silent; the same scan keeps running.

**Honest image downloads:** when only a preview is available, results are marked thumbnail_only; a larger original beside it is preferred, and a locked original asks for image access instead of saving the tiny preview as success.

# What's new in 0.1.90

**Faster ordinary commands:** after setup is already complete, resolve / groups / query / search no longer spend seconds re-checking the chat-app conversion on every run.

**Honest cache freshness:** local decrypted snapshots still miss when the live archive or its write-ahead log changes, including same-length rewrites that keep the old timestamp.

# What's new in 0.1.89

**Snappier chat search:** repeat lookups reuse local contact/group info, multi-word themes can be searched in one pass, and expanding nearby messages no longer drops the original matches.

**Clearer answer ranking:** when you need the real conclusion among noisy acknowledgements, the agent can use the product's optional answer-ranking path; ordinary search stays on this computer.
