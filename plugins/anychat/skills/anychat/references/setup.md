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

That JSON is the only first-run contract. `doctor --json` may show a short product status (`status`, `support_code`, `say_to_user`); it is not a second state machine. Do not run leftover first-run verbs. Provision never updates AnyChat itself; an explicit update request uses `update --json` and the verified official release package, never a Git marketplace refresh.

## You do (agent-owned)

1. For an explicit setup request, run `provision --json`. For an ordinary query or media request, run that request first and follow the exact typed continuation it returns; do not replace the original intent with a separate hand-written flow.
2. On `needs_agent`, do not speak setup narration to the human. Machine requirements belong in `needs`; do not invent a version, path, or download.
3. If `status` is `needs_agent`, satisfy each typed item in `needs`. Search the host using the listed `obtain` methods and your available computer/filesystem capabilities. Then send one `anychat.provision.supply.v1` JSON document to the exact `continue_args` (which includes `--supply-stdin`) over stdin; never place a path or resume token on argv or ask the human to locate or transcribe one. When the envelope belongs to an ordinary request, preserve its opaque `resume_token` in this protected stdin document.
4. If `status` is `needs_human`: wait for the human's decision or unavoidable action. If they decline, stop. If `after_human_action` is `satisfy_needs_then_continue`, first satisfy every typed item in that same envelope's `needs`—including performing an approved installation or mapping the human's visible account choice to the protected local account input—and only then invoke the exact `continue_args`. Never invoke the continuation before the consented machine work is complete. For any other approved human action, run the exact `continue_args`. Do not invent flags.
5. If `status` is `ready`, invoke its exact continuation and pipe `resume_token` over stdin. AnyChat resumes the sealed original request; do not rebuild it from memory. Only say the archive is ready when setup itself was the user's explicit request.
6. If `status` is `blocked` and `offer_tell_jacky` is true: then and only then draft Tell Jacky, run `feedback preview`, show the exact draft, and send after approval with both `--user-confirmed` and that preview's `--confirmation-binding`. Do not automatically rerun the identical invocation.
7. Never invent a location or accept a candidate yourself: AnyChat validates every supplied fact locally. On both macOS and Windows, if AnyChat requests app installation or replacement, use only the exact platform package and verification facts in `needs[].installer`; never use the vendor's current download or another build. Get the required consent, quit every running instance yourself, perform the installation, open exactly one validated instance, and let AnyChat recheck it. Never loop automatically. There is no fixed attempt limit. Compare `progress_fingerprint` and `no_progress`; a repeat count alone is never proof of a dead loop, and `dead_end` is the only terminal no-way-forward signal.

Supply shape (agent-to-CLI only):

```json
{
  "schema": "anychat.provision.supply.v1",
  "for_progress": "<progress_fingerprint>",
  "resume_token": "<opaque token when continuing an ordinary request>",
  "supply": {
    "archive_root": ["<absolute candidate directory>"],
    "chat_app_path": "<absolute application bundle or executable>",
    "archive_account": "<validated local account selected after the human identifies the visible account>"
  }
}
```

Send only keys requested by `needs`; use an empty `supply` object when the continuation requests no fact. `archive_root` may contain multiple candidates. Rejected candidates return only key/index/result code—never a path. A stale `for_progress` is rejected so facts from an older machine state cannot silently mutate the current setup.

Always invoke the exact `continue_args` returned for the current progress state. Do not invent flags. An account choice is personal input; never guess it.

## Human may only

| `human_action` | What you tell them (must match `say_to_user`) |
|----------------|-----------------------------------------------|
| `approve_admin` | 电脑弹出了管理员确认。请点允许或输入这台电脑的密码。 |
| `agree_material_change` | AnyChat 说明需要修改什么及如何恢复；用户只回答是否同意。 |
| `select_account` | 这台电脑上有多个聊天账号。请告诉我用哪一个平时聊天的。 |
| `retry_permission` | 上一次管理员确认已取消。用户只回答是否重新打开一次确认窗口。 |

This table is the complete set of values the CLI may emit as `human_action`. Opening, quitting, reducing extra instances, installation, retry after a changed machine state, folder discovery, and application discovery belong to the host agent. `retry_permission` authorizes only reopening a previously cancelled operating-system prompt; the host agent performs the retry. If the host agent opens the chat app and its own UI asks for account sign-in, the human completes only that sign-in. You do not ask the human to type a command, find a path or log, read a version, install software, or open or quit an application.

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

Report only a human decision or irreducible action, then the requested result. Never narrate background work or repeat machine-facing output to the human.

## Never

- Ask the human to open Terminal or type a command.
- Ask the human for a path or raw log. A typed path need is work for the host agent, not the human.
- Walk a first-run state machine or invent extra CLI verbs.
- `git clone` the plugin marketplace.
