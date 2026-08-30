# AnyChat 0.1.40

## User-visible changes

- Your AI helper now owns first-time setup. AnyChat tells it exactly what is missing; the helper finds the chat app and archive, checks the computer, and supplies the result locally. You only handle unavoidable password, permission, or consent windows.
- Setup no longer gives up after a fixed number of attempts. It records unchanged progress for the host agent and stops only at a proven dead end; it never erases a pending human permission, sign-in, or consent step.
- All chat-app versions from 4.1.10 onward are accepted without an upper ceiling. The verified macOS 4.1.5 build 31960 remains supported, and AnyChat never sends a newer installation to an older installer.
- Diagnostic reports keep safe OS, build, architecture, app-version, permission-result, and count information so the developer can investigate. Passwords, secrets, local paths, process IDs, and chat content still stay out.
- Official releases now use one machine-owned lightweight-tag command. It refuses version drift, occupied tags, non-main commits, missing native Windows acceptance, and annotated tags before publication starts.

# AnyChat 0.1.37

## User-visible changes

- First-time setup no longer stops after two failed attempts. AnyChat never retries in an automatic loop, but it will try again whenever the human completes the requested computer action or explicitly asks.
- Windows Administrator approval now preserves OneDrive locations and sends only the real chat-app main process to the local access step, fixing a failure where approval succeeded but no local archive was found. If approval succeeds but process access still fails, AnyChat reports that separately instead of asking the user to approve the same window again.
- Bug reports now include safe OS/version/build, app-version, permission-outcome, and count diagnostics by default. Old diagnosis files are not attached to new reports; passwords, paths, process IDs, and chat content still stay out.

# AnyChat 0.1.36

## User-visible changes

- On Windows, installing the supported chat app and opening the local archive now ask for Administrator approval when the computer requires it. Click Allow if a window appears.
- If this computer already has the supported chat-app version, AnyChat does not overwrite it again.
- First-time setup names the real problem (missing local archive, or Administrator approval) instead of saying the helper could not see the same files.
- Your helper now has one activate path. It will not ask you to tell Jacky unless setup is actually stuck. You at most install, open, or quit the chat app, and click the computer's password or Allow window.

# AnyChat 0.1.35

## User-visible changes

- First-time setup is one step. The helper runs it; you keep the chat app open and click at most one system password or permission window.
- If the chat app on this computer is the wrong version, AnyChat names the exact supported version. After you agree, it overwrites the app only and leaves chat history in place.
- If this copy of AnyChat is old, it updates itself from the official plugin ZIP before trying to open the archive. It will not keep retrying a failed setup.
- Feedback written in Chinese on Windows is sent as UTF-8, so the report is readable.

# AnyChat 0.1.34

## User-visible changes

- Windows first-time setup finds the local chat archive by what the files look like, not by a guessed folder name. If the chat app is open and the data is on this computer, AnyChat should find it.
- Official Mac builds hide the builder’s folder paths and require Apple’s hardened runtime when signed.
- If a plugin marketplace refresh times out cloning GitHub, the helper uses the official ZIP snapshot instead of asking you to invent a workaround.

# AnyChat 0.1.33

## User-visible changes

- Windows first-time setup finds the local chat archive by what the files look like, not by a guessed folder name. If the chat app is open and the data is on this computer, AnyChat should find it.
- Official Mac builds hide the builder’s folder paths and require Apple’s hardened runtime when signed.
- If a plugin marketplace refresh times out cloning GitHub, the helper uses the official ZIP snapshot instead of asking you to invent a workaround.

# AnyChat 0.1.32

## User-visible changes

- Write down what was decided. Confirmed sentences live with the account; chat text stays on this computer. Another computer with the same login can read the sentences.
- `notes save / list / show / touch / rm`. Save only after you confirm. At most 5 new sentences per write. An old sentence cannot be silently edited. Deleting a topic also deletes its notes.
- If this computer has no chat archive, AnyChat still shows the sentences and says the original messages are not here.

# AnyChat 0.1.31

## User-visible changes

- Follow a named topic (a person or a group, plus what you care about). New messages are found on this computer; the topic and who-is-who live with the account so another computer can follow the same thing. Chat text is not uploaded.
- `topic save / list / show / check / rm`. A topic cannot be keyword-only. v1 cannot rename. Checking for new messages needs you to be signed in.
- People you already linked move to the account the first time this version talks to it. After that, searching by person also needs a connection.

# AnyChat 0.1.26

## User-visible changes

- First-time setup now diagnoses, then asks the human to install the supported chat app. Chat databases are never touched.
- Recommended installers are hosted: macOS 4.1.5 and Windows 4.1.9. Already-readable archives are not force-downgraded. Prepare-access retries at most twice.

# AnyChat 0.1.25

## User-visible changes

- Product Signals now cover the full command funnel: search, export, media list/download, friends, groups, sessions, doctor, and completed setup. Fail-open, calling-thread, 500ms.

# AnyChat 0.1.24

## User-visible changes

- Authenticated searches now emit Product Signals (`search_results_returned`) by default. Emission is fail-open and runs on the calling thread so the CLI does not exit before the event is posted.
- Direct first-time setup on Windows stops at 4.1.10.30; 4.1.10.31+ fail closed instead of being sent into first-time access.

## Package coverage

- macOS Apple Silicon: native binaries for `anychat` and `anychat-access`.
- Windows x64: binaries for `anychat.exe` and `anychat-access.exe`.
- Runtime manifests pin each artifact by SHA-256 and fail closed on unsupported platforms or checksum drift.
