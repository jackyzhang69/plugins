# AnyChat — what changed in 0.1.94

- On Windows, opening the original picture waits for the on-computer confirmation instead of saying it is ready and looping.
- A smaller local preview is no longer saved as the original while a sharper file still needs that confirmation.
- Empty or mismatched attachments are retried against the exact cached file on this computer. If that file was never saved here, the download says so instead of handing back a different file.
