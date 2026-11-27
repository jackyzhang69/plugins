# What's new in 1.9.15

This release republishes the 1.9.14 improvements with corrected marketplace tagging: `plugin-v1.9.15` points to the exact `release(formbro): 1.9.15` commit.

- Use `formbro import apply-json --allow-incomplete` to save a case before all the client's information is available. The result lists what is still missing.
- New-case imports reject values still written in Chinese, Japanese, or Korean until they are translated into English. Native-name fields and the case name are exempt.
