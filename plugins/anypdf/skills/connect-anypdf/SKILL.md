---
name: connect-anypdf
description: Connect the AnyPDF public client through a safe stdin credential flow.
---

# connect-anypdf

Use this skill to establish the local AnyPDF public-client connection. This is
an agent-native, non-interactive workflow; never ask a user to paste a token in
the prompt, an issue, a report, or a command argument.

## Workflow

1. Run `anypdf doctor --json` first. Treat `status: not_connected`, a missing
   backend, or an unavailable server 69 as an honest local/unconnected state;
   do not claim that production filling is available.
2. When the user has supplied an approved backend URL and a token through a
   secure host channel, run:

   ```sh
   printf '%s\n' "$ANYPDF_TOKEN" | anypdf auth login --backend-url "$ANYPDF_BACKEND_URL" --token-stdin --json
   ```

   Prefer an approved secret stdin injection or environment injection. Do not
   put the token in the prompt or argv. Non-loopback backends must use HTTPS;
   HTTP is only valid for localhost/127.0.0.1/::1 development.
3. Run `anypdf auth status --json` to inspect local configuration only. It does
   not contact the backend and never prints the token.
4. Use the existing AnyPDF forms, validate, fill, intake, and feedback skills
   only after the connection reports configured/connected. Configuration is
   stored in the OS-adapted private connection file with restrictive mode.

## Safety boundary

`auth clear` is idempotent and removes only the local connection file. The
public plugin is an HTTPS client; it contains no native PDF engine, templates,
server-owned mapping data, admin workflow, or secret. If server 69 is not deployed or
the backend cannot be reached, report that fact and stop at the unconnected
boundary.
