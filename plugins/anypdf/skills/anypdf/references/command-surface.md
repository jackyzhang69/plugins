# Public CLI command paths (coverage reference)

Not for first-session orientation. Host agents discover the live surface via
`"$ANYPDF" commands --json` and translate it for the human.

Public commands: `login`, `whoami`, `logout`, `doctor`, `read`, `validate`,
`forms catalog`, `forms resolve`, `forms schema`, `fill submit`,
`fill readiness`, `fill status`, `fill download`, `intake submit`,
`feedback submit`, `feedback status`, `knowledge list`, `knowledge add`,
`knowledge remove`, `resume --envelope-stdin`, `commands`.

When an ordinary command returns `jz.plugin.envelope.v1` with
`status=needs_agent`, complete its requested agent step and pipe that exact JSON
object to `anypdf resume --envelope-stdin`. Never reconstruct the original
arguments: the client resumes its sealed private request, including after a
login and from a different working directory.
