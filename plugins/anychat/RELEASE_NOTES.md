# AnyChat 0.1.45

## User-visible changes

- Setup keeps computer discovery, app installation, app lifecycle, and local-path handling with the host agent; the human only confirms a material change, signs in, or completes an operating-system password prompt.
- Every setup starts from the exact AnyChat-provided platform build unless that exact validated build is already installed. The stopped app is rechecked before first launch so a background replacement cannot become the setup baseline.
- Mac administrator approval now uses the visible system password window. Cancelling pauses safely, while temporary local-access failures remain retryable without another conversion consent or an arbitrary attempt limit.
- Mac archive access now hands encrypted database page samples to the privileged helper without asking that helper to cross the operating system's protected app-data boundary.
- Image downloads produce directly previewable JPEG or PNG files. Validated account-bound image access is reused across CLI invocations, so later downloads do not repeat the expensive first derivation.
- Tell Jacky diagnostics retain operating-system, app-build, permission, scan-progress, and failure evidence while continuing to exclude chat content, identities, local paths, and access material.
