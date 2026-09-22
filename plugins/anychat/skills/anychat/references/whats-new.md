# What's new in 0.1.90

**Faster ordinary commands:** after setup is already complete, resolve / groups / query / search no longer spend seconds re-checking the chat-app conversion on every run.

**Honest cache freshness:** local decrypted snapshots still miss when the live archive or its write-ahead log changes, including same-length rewrites that keep the old timestamp.

# What's new in 0.1.89

**Snappier chat search:** repeat lookups reuse local contact/group info, multi-word themes can be searched in one pass, and expanding nearby messages no longer drops the original matches.

**Clearer answer ranking:** when you need the real conclusion among noisy acknowledgements, the agent can use the product's optional answer-ranking path; ordinary search stays on this computer.

# What's new in 0.1.88

**Faster group search:** looking up what someone said across groups reads several groups at once instead of one by one.

**Cloud Agent skills path:** in-repo AGENTS.md and plugin/skills are the source for Cursor Cloud; do not expect mac ~/.agents sync.
