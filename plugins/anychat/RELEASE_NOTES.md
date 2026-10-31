# AnyChat 0.1.104

- Media list and download now report what actually went wrong (missing local file, decode failure, unwritable output folder, archive unavailable) instead of a generic query failure. A batch whose only gaps are attachments never cached on this computer now succeeds and reports how many files were copied.
- Windows archive setup finds every data folder even when an older archive sits next to the current one, and an archive folder plus account supplied together are now accepted together instead of the account being rejected.
- Product signals now carry redacted diagnostics: on failure, a scrubbed error message and stack; on every operation, timed steps plus basic client facts (OS, architecture, AnyChat version, locale). Chat content, contact names, queries, file names, paths, emails and secrets are removed on this computer before anything is sent.
