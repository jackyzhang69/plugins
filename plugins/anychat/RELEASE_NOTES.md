# AnyChat 0.1.95

On Windows, a picture that WeChat already saved in the same chat's bubble or thumbnail cache is downloaded from that cache when the attachment folder is empty. A file from a different chat is not used in its place.

On Windows, original-picture export waits for the on-computer confirmation instead of looping, and a lower-quality sibling is not saved as the original while a sharper file still needs access. Empty or mismatched attachment downloads retry the exact cached file on this computer, and refuse a different file.

Accepts freshly exchanged account tokens even when the computer clock is wrong.

On Windows, after the supported chat app is installed, AnyChat pauses its automatic replacement before the first open. Signing in no longer swaps that app for an unsupported newer build and then blocks local archive setup.
