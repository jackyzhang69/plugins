# AnyChat — what changed in 0.1.98

- On Windows, after the supported chat app is installed, pausing automatic replacement no longer stops setup. If the pause needs another Allow click, AnyChat asks for that and continues; it does not treat a missing updater folder or a stopped chat app as a dead end.
- On Windows, opening a picture no longer asks for the same computer permission again after that permission was already allowed and the picture key was still missing. That path now stops instead of looping.
- Picture keys for the current Windows chat app can be recovered from this computer's account data, not only from a long memory scan.
- Checking status, resolving a name, and previewing a note to Jacky come back with a next step instead of sitting silent while the rest of the history is still being checked. Status says when a remaining-history scan is needed.
