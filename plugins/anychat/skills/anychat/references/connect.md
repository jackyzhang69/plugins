# Connect AnyChat

## Product contract

- One free Portal login works across every official plugin. If AnyChat is
  already connected, continue without asking again.
- The host agent performs connection for the human. The human never opens a
  terminal, runs a command, finds a credential file, or repeats a credential
  already available to the host.
- AnyChat owns credential validation and storage. Do not explain its private
  authentication flow, storage layout, endpoints, or token transformation.

## Host-agent flow

1. Run `"$ANYCHAT_BIN" whoami --json`. If connected and entitled, silently
   continue the original request.
2. Otherwise ask for either a readable local file containing the Portal token
   (preferred) or a pasted token. Do not ask the human to run anything.
3. Read the value through the agent tool channel and pipe it to:

```bash
"$ANYCHAT_BIN" login --token-stdin --accept-personal-use
```

4. The value goes over stdin only. Never put it on argv, repeat it in chat,
   print it, log it, screenshot it, or include it in feedback.
5. Confirm connection using masked product output, then resume the user's
   original request. Do not require the human to ask for setup separately.

If the human pasted the value into chat, warn once that a local file is safer
next time, then continue immediately. Never refuse a valid current request just
because the safer delivery method was not used.

## Advisory update check

On first AnyChat use in a host-agent session, `doctor --check-upgrade --json`
may provide an advisory update. Recommend it briefly, do not block the request,
and never auto-update. Updating AnyChat remains an explicit host-plugin-manager
action.

## Talk to the human

Use only: “需要先登录一次免费账号” or “登录成功，我继续处理刚才的事情。”
Do not describe credentials, endpoints, local files, internal errors, or CLI
mechanics unless one plain product support code is necessary for the next safe
action.
