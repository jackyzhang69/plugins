# Public CLI command paths (coverage reference)

Not for first-session orientation. Host agents discover the live surface via
`anymail commands` (includes `capabilities[]`) and translate it for the human.

Every public path below must stay listed as a contiguous token sequence so
Direction B skill-surface checks can verify coverage against `commands --json`.

- `version`
- `commands`
- `doctor`
- `login`
- `logout`
- `whoami`
- `feedback create`
- `feedback list`
- `feedback status`
- `feedback inbox`
- `feedback read`
- `account list`
- `account discover`
- `account open`
- `account open-google`
- `account open-microsoft`
- `account upgrade-google-send`
- `account upgrade-google-modify`
- `account upgrade-google-settings-basic`
- `account upgrade-microsoft-send`
- `account upgrade-microsoft-readwrite`
- `account reauthorize-google`
- `account reauthorize-microsoft`
- `account reauthorize-imap`
- `account revoke-google`
- `account revoke-microsoft`
- `account remove`
- `account status`
- `mail mailboxes`
- `mail search`
- `mail search-intent`
- `mail thread-intent`
- `mail thread-search`
- `mail thread-get`
- `mail history-intent`
- `mail history-list`
- `mail profile-get`
- `mail handling-list`
- `mail handling-remember`
- `mail handling-forget`
- `mail memory-list`
- `mail memory-remember`
- `mail memory-forget`
- `mail fetch`
- `mail attachment`
- `mail flag`
- `mail move`
- `mail copy`
- `mail draft create`
- `mail draft reply`
- `mail draft forward`
- `mail draft push-cloud`
- `mail draft cloud-list`
- `mail draft cloud-delete`
- `mail draft read`
- `mail draft update`
- `mail draft attach`
- `mail draft detach`
- `mail draft delete`
- `mail draft prepare`
- `mail draft confirm-send`
- `mail draft dispatch-confirmed`
- `mail draft status`
- `mail trash`
- `mail archive`
- `mail junk`
- `mail batch-trash`
- `mail restore`
- `mail permanently-delete`
- `mail export`
- `mail import`
- `mail labels`
- `mail label-create`
- `mail label-get`
- `mail label-update`
- `mail label-delete`
- `mail label-modify`
- `mail filters`
- `mail filter-get`
- `mail filter-create`
- `mail filter-delete`
- `mail vacation-get`
- `mail vacation-set`
- `mail folder-create`
- `mail folder-rename`
- `mail folder-move`
- `mail folder-delete`
- `mail signature-get`
- `mail signature-set`
- `mail signature-clear`

## Doctor install status

`doctor` reports three separate facts. Do not treat an absent canonical
install as a broken CLI.

- `common.install_home` is only the canonical `plugins/anymail/current`
  package tree (`scope: "canonical_install"`, `is_runtime_home: false`).
  `cli_usable_when_absent: true` means a plugin-cache or other verified
  CLI can still open and inspect accounts when `present` is false. The
  cached CLI does not make `present` true. `running_version` is the CLI
  that produced the report; `aligned` is true only when that version
  matches the installed package. A cached CLI that is newer or older than
  the canonical install does not make `aligned` true.
- `common.runtime_home` is the plugin runtime directory for account and
  journal state. It is not the package install tree.
- `common.running_cli` reports this process: `usable`,
  `canonical_install_required: false`, and `origin` of
  `canonical_install`, `plugin_cache`, or `other`.

When `install_home.present` is false or `install_home.aligned` is false
and the operator wants the canonical package tree, run the current
verified package binary with `doctor --repair-install`. The repair
validates the package containing that binary, then installs it at the
canonical path.

## Draft create stdin

`mail draft create`, `reply`, `forward`, and `update` publish the exact
accepted stdin schema on `anymail commands`. Validation errors name the
field and expected type. Syntactically valid JSON that does not match
the schema is not reported as invalid JSON.
