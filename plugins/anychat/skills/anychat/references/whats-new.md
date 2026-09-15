# What's new in 0.1.86

**Setup gate fix:** queries no longer fail just because an old WeChat setup marker is missing or stale. Live source readiness decides what can run. If a host is stuck after 0.1.85, ask the agent to run the setup repair flow from live archive evidence instead of editing config by hand.

**Matrix / coverage honesty:** WeChat search results distinguish Unknown, known-unmatched, and matched Partial more clearly, keep multi-install observations, and continue bounded scan batches (including zero-hit) so coverage reflects what was scanned—not a claim that every historical message was recovered.

**From 0.1.85:** Windows provision shows the UAC prompt when elevation is required; archive MAC checks skip the SQLite reserved lock-byte page and isolate soft-fail shards.
