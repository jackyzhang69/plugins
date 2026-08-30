# 开通本机档案

## Goal

开通这台电脑上的**本机档案**。 The human never uses a terminal. You absorb every problem.

## Locked words

Speak these. Do not invent synonyms in chat.

| Say | Means |
|-----|--------|
| 开通本机档案 | First-time Activate of the local chat archive on this computer |
| 本机档案 | That archive, already on this computer |
| 聊天软件 | The chat app on this computer |
| 覆盖安装 | Replace the app only; chat history stays |
| 管理员确认 | Password window, Administrator / Allow, or Open Anyway |
| 告诉 Jacky | Last resort only — see the gate below |

**Ban in your own words:** 连接 (that is Portal login, not this step), 破解, Terminal, 命令, and any specific social-network name. Repeat `say_to_user` instead of naming a network.

## One verb

```bash
"$ANYCHAT_BIN" provision --json
```

That JSON is the only first-run contract. `doctor --json` may show a short diagnosis (`state`, `support_code`, `say_to_user`); it is not a second state machine. Do not run leftover first-run verbs. If a marketplace GitHub clone times out, `provision` uses the official ZIP; never `git clone` the plugin repository.

## You do (agent-owned)

1. Run `provision --json`.
2. Speak only `say_to_user` to the human. If `recommended_version` or `recommended_installer_url` is present, that wording is already in `say_to_user` — repeat it; do not hide the version or invent another download.
3. If `status` is `needs_human`: wait until they finish `human_action`, then run the exact `continue_args`. Do not invent flags.
4. If `status` is `ready`: 本机档案已经可以用了. Optionally confirm with a short friends list.
5. If `status` is `blocked` and `offer_tell_jacky` is true: then and only then draft Tell Jacky, show the draft, and send after `--user-confirmed`. Do not automatically rerun the identical invocation.
6. Never download or run an installer yourself. Never ask for a path or a log. Never loop automatically. There is no fixed attempt limit: after the human completes a requested GUI action, changes the environment, or explicitly asks you to try again, run the exact `continue_args` again. A repeat count alone is never proof of a dead loop.

`--install-app-consent` means they agreed to 覆盖安装. The CLI downloads and overwrites the app only. If this computer already has the supported version, the CLI skips that overwrite and continues 开通.

`--resign-consent` means they agreed to one local re-sign. The computer will ask for 管理员确认. After it finishes they reopen 聊天软件 and sign in.

`--restore-app` means restore the official app after a failed re-sign. They must fully quit first.

`--account-id` is only for `select_account`. Do not guess.

## Human may only

| `human_action` | What you tell them (must match `say_to_user`) |
|----------------|-----------------------------------------------|
| `agree_install_app` | 必须换成我们支持的版本才能开通。同意后我来覆盖安装（只换应用，聊天记录不动）。 |
| `open_chat_app` | 请打开聊天软件并登录你平时用的账号。打开后告诉我一声。 |
| `quit_chat_app` | 请先完全退出聊天软件。关闭窗口不够，要从菜单或托盘选择退出。 |
| `quit_extra_chat_app` | 请只留一个聊天软件开着——就是你平时用的那个——把另一个完全退出。 |
| `approve_admin` | 电脑弹出了管理员确认。请点允许或输入这台电脑的密码。 |
| `agree_resign` | 开通需要给这台电脑上的聊天软件重新签名一次。你同意的话，电脑会弹出密码窗口。 |
| `select_account` | 这台电脑上有多个聊天账号。请告诉我用哪一个平时聊天的。 |
| `retry` | Repeat `say_to_user`. The prior attempt and any prior window are over; run `continue_args` only after they explicitly ask to continue. |

No other human work. You do not ask them to type a command, pick a folder, read a version, or “tell Jacky” while a `human_action` is still open.

## 管理员确认

On Mac: a password window or Open Anyway. On Windows: an Administrator / Allow window. You never collect the password. After they click, run `continue_args` yourself. If a later human-requested attempt needs the system window again, it may appear again; do not stop because of an earlier attempt count.

If Windows reports `E_WINDOWS_PERMISSION_CANCELLED`, the prior window is already closed. Say that it was cancelled and wait; only a human-requested retry may reopen it. If it reports `E_WINDOWS_ACCESS_DENIED_AFTER_APPROVAL`, the approval succeeded and the later access failed—do not blame the click or ask them to approve the closed window again.

## Tell Jacky gate

Default is off.

Offer Tell Jacky only when **all** of these are true:

1. `provision --json` returned `status: blocked`
2. `offer_tell_jacky` is true
3. There is no remaining `human_action` for them to do

Do not offer it on `needs_human`. Do not offer it because you are unsure. Do not offer it on the first password / Allow click. Solve with the table above first.

## Talk to the human

Report only major stages: need login → need 聊天软件 open → one 管理员确认 if asked → 本机档案已经可以用了. Never mention keys, databases, helper names, or storage paths.

## Never

- Ask the human to open Terminal or type a command.
- Ask the human for a path or a log. During setup, detect versions yourself; when drafting a confirmed bug report, follow Tell Jacky's required safe environment checklist if automatic diagnosis is missing.
- Walk a first-run state machine or invent extra CLI verbs.
- `git clone` the plugin marketplace.
