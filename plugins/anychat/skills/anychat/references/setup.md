# AnyChat setup

## Goal

Enable local archive access for **this user on this computer**. The human
never uses a terminal. You absorb every problem.

## Host-agent contract (mandatory)

1. The human **never uses a terminal**. Never say “run”, “命令”, “Terminal”, or paste CLI to the human.
2. Run **one** command:

```bash
"$ANYCHAT_BIN" provision --json
```

3. Speak only `say_to_user`. Do not name a specific social app yourself — repeat the CLI wording. If `recommended_version` is present, that is the exact supported version; other versions are unsupported. If `recommended_installer_url` is present, give that download link only after repeating `say_to_user`.
4. If `status` is `needs_human` and `human_action` is `agree_install_app`: wait until they agree, then run `continue_args` (includes `--install-app-consent`). The CLI overwrites the app only. You do not download or run an installer yourself.
5. If `human_action` is `select_account`: ask which of the named accounts they actually chat with, then run `provision --json --account-id <that id>`. Do not guess.
6. For any other `needs_human`: wait until they finish `human_action`, then run the exact `continue_args`. Do not invent extra first-run verbs.
7. If `status` is `ready`: the archive is usable. Optionally confirm with a short friends list.
8. If `status` is `blocked` and `offer_tell_jacky` is true: draft Tell Jacky yourself, show the draft, send only after `--user-confirmed`. Do not retry provision for the same failure.
9. Never compare chat-app versions yourself. Never loop. The CLI owns the attempt budget.

## Talk to the human

Report only major stages (need login → need the chat app open → one password/Open Anyway click if asked → ready). Say “还没准备好本机档案 / 已经可以用了”. Repeat `say_to_user` when it includes a recommended version and download URL. Never mention keys, databases, helper names, or storage paths. Never name a specific social network.

## Never

- Ask the human to open Terminal or type a command.
- Ask the human for a version number, a path, or a log.
- Walk a first-run state machine or invent extra CLI verbs. `provision` is the only first-run verb.
- `git clone` the plugin marketplace. If an update is needed, `provision` uses the official ZIP.
