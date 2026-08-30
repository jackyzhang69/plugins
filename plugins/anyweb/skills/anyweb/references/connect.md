# Connect

Run `anyweb doctor --json` first. If the shared user credential is configured,
do not ask again.

The host agent performs connect for the human. Prefer a file containing the
Portal token and pipe it to `anyweb login --token-stdin --json`. A chat paste is
allowed after one warning that a file is safer. Never pass a token on argv,
print it, log it, or tell the human to open a terminal. Confirm with masked
`anyweb whoami --json`; `logout` removes only the shared documented user slot.

AnyWeb exchanges `aud=anyweb` and keeps the short-lived JWT only in memory. Its
stable opaque account binding contains no account identifier and lives only in
the AnyWeb runtime directory.
