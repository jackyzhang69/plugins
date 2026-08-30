# Tell Jacky

Draft the exact AnyWeb feedback first: type (`feature-request`, `bug-report`, or
`knowledge-tip`), title, and description. Remove credentials, client data,
website values, local paths, and screenshots. Show the full draft and wait for
the user's explicit approval.

Only after approval run `anyweb feedback create --type <type> --title <title>
--description <description> --user-confirmed --json`. Never infer confirmation.
Use `feedback list`, `feedback status --id <id>`, `feedback inbox`, and
`feedback read --update-id <id>` for the same AnyWeb inbox. Explain results in
plain language; do not paste internal JSON.
