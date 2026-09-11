# Saved website login

Website login and filling stay on this computer. Saving a login only stores an
encrypted copy so a later local task can reuse it. Do not copy a website
username, password, or security answer unless the human explicitly chooses to
save that login for this site and role. A generic local setup request is not
consent to save a login.

Never ask the human to paste a website username, password, or security answer
into chat. Never read, create, edit, summarize, log, or inspect the secret
input. The human must place the exact input in a user-controlled local file;
after they identify that file, redirect it to the CLI without reading it. The
CLI accepts secrets only from redirected stdin and returns no stored value.

For the first save, explain that AnyWeb will keep an encrypted copy for reuse
on this computer. After explicit approval run:

```bash
"$ANYWEB_BIN" hosted-account set --site ircc-ee-profile --role default --credentials-stdin --hosted-custody-confirmed --json < <USER_CONTROLLED_FILE>
```

For the ordinary user filling their own Express Entry profile, the site is
`ircc-ee-profile` and the role is `default`. Do not invent another role.
The credentials file must contain only `username` and `password`. Do not keep
or delete the user's file; its lifecycle remains the user's decision. Use
`hosted-account status --site ircc-ee-profile --role default --json` for
masked state and `hosted-account forget --site ircc-ee-profile --role default
--user-confirmed --json` only after separate explicit deletion approval.

When an active website session returns a current visible static security
question plus an opaque challenge reference, show only that visible question
to the human. They place an input containing only `answer` in their own local
file, then the agent redirects it to `hosted-security-answer set
--challenge-ref <ref> --answer-stdin --json`. Never invent a question, send
question text with the answer, or use this path for a one-time code, MFA,
CAPTCHA, recovery code, or unknown challenge.

A new answer is not reusable until the current website proves it worked. If it
fails or the page changed, stop and ask the human; never retry automatically.
Use `hosted-security-answer status` for masked counts and
`hosted-security-answer forget --user-confirmed` for explicit deletion.
