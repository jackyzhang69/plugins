# Public CLI command paths (coverage reference)

Not for first-session orientation. Host agents discover the live surface via
`"$ANYWEB_BIN" commands --json` and translate it for the human. The Codex
host loop for step waits and resume is MCP (`anyweb mcp serve`), not
skill→CLI `task status` / `task resume` hang-restart.

Public commands: `doctor`, `login`, `logout`, `whoami`, `repair claims`,
`hosted-account set`, `hosted-account status`, `hosted-account forget`,
`hosted-security-answer set`, `hosted-security-answer status`,
`hosted-security-answer forget`,
`task start`, `task status`, `task resume`, `task cancel`, `task result`,
`task forget-answers`,
`runtime doctor`, `runtime install`,
`feedback create`, `feedback list`, `feedback status`, `feedback inbox`,
`feedback read`,
`pair join`, `pair status`, `pair snapshot`, `pair inbox`, `pair read`,
`pair result`, `pair close`,
`mcp serve`, and `commands`.

The `hosted-account` and `hosted-security-answer` commands save or forget a
login for reuse on this computer (local vault sqlite). They are the product
credential cache. Do not use Keychain `anyweb credential set` for that path.
They do not move website work off this
machine. An active fill that still needs a password, email code, or security
answer continues through MCP `task_resume`, not with those save commands
and not by shelling CLI `task status` / `task resume` as a host loop. For the
ordinary Express Entry user, use site `ircc-ee-profile` and role `default`.
Compile `local_inputs` from `anyweb-catalog requirements --pin` before `task start`.
