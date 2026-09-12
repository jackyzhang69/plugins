# AnyChat 0.1.81

## User-visible changes

- Claude Code can load the plugin again. The extra hooks declaration that blocked 0.1.80 is gone.
- If the verified chat-app installer downloaded correctly but would not start, AnyChat now tells you to double-click that same package. It does not call a hash-matched package damaged, and it does not repeat the same wait with no progress.
- A previously readable archive is no longer wiped when a later setup step fails. If setup cannot finish, it still leaves a way to continue instead of a dead end, and it does not ask you to pick an account when only one archive is on the computer.
