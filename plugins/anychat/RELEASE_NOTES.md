# AnyChat 0.1.44

## User-visible changes

- Opening a local archive now verifies every discovered message shard, so a contacts-only partial setup cannot be reported as ready.
- Windows image downloads, including batches, can pause for protected local access and resume the exact request without duplicating verified files.
- Setup always converges on the two verified AnyChat-provided platform packages when the installed chat app is not already an exact match.
- Setup no longer stops after an arbitrary number of attempts; it stops only on a proven terminal condition or explicit human refusal.
- Tell Jacky previews include broader safe machine diagnostics while continuing to exclude chat content, identities, paths, and access material; sending requires the exact one-time preview binding.
