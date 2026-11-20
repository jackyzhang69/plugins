# What's new in 1.3.0

Scoring now requires an internet connection and AnyCase sign-in to fetch
published scoring rules. Applicant inputs are still evaluated on your device,
and `score diff` works offline.

# What's new in 1.2.13

AnyCase now reports basic usage for program, eligibility, document-list, and scoring commands: the operation, public program or policy identifier when applicable, and success or failure. Applicant profiles, documents, and file paths are not included. Local eligibility and local document-list evaluations do not report usage.

macOS and Windows packages remain unsigned and not notarized.

# What's new in 1.2.12

macOS and Windows packages are unsigned and not notarized. Your operating system may show a security warning during installation.

Atlantic Immigration Program checks: a study program delivered exactly half by distance learning is now reported as not meeting the in-person requirement (IRPR 87.3(5)(a)(iii) and IRCC agree on this), instead of unresolved. Exactly half-time language study stays unresolved because the regulation and IRCC's page word the threshold differently.

# What's new in 1.2.11

macOS and Windows packages are unsigned and not notarized. Your operating system may show a security warning during installation.

Atlantic Immigration Program checks now follow IRPR 87.3 more closely: work TEER comes from the NOC 2021 code, settlement funds are measured on the PR application date, and work authorization or exactly half-time language study stay unresolved instead of guessed. A missing fact inside a minimum, or a blank text answer, is now reported as unknown, and semantic rule reasons name the proposition that was tested.

# What's new in 1.2.10

macOS and Windows packages are unsigned and not notarized. Your operating system may show a security warning during installation.

Program descriptions now report unavailable scoring without treating it as an unknown program. Federal document assessment annotations use the evaluated list's source references, and Atlantic eligibility treats TEER 2 and 3 as equal levels under IRPR 87.3(7).

# What's new in 1.1.21

`anycase documents` now distinguishes newer source reviews and unavailable or uncertain source baselines from the active immutable document-list pin, while source updates do not silently switch that guide.

# What's new in 1.1.20

Low-wage LMIA pre-check now uses the official CMA unemployment table when the city is known, requires eight weeks of advertising, and will not silently score an LMIA file as BC PNP.

# What's new in 1.1.19

`anycase` introduces multi-program auto-discovery catalog with `programs list` and `programs describe <id>`, eliminating silent fallbacks and requiring a named program argument before evaluation.

# What's new in 1.1.18

`anycase check-eligibility` evaluates Nova Scotia Nominee Program (NSNP) streams with modernized 2026 application guides and declarative schemas.

# What's new in 1.1.17

`anycase check-eligibility` evaluates federal LMIA streams with Statistics Canada CMA unemployment and provincial median wage refusal matrices.

# What's new in 1.1.16

`anycase pnp` searches official Provincial Nominee Program policies and guides with isolated provincial scope.

# What's new in 1.1.15

Published marketplace packages verify cleanly without shipping a source Cargo manifest.

# What's new in 1.1.14

Search results now say when they were not checked against what you asked, and slow searches are given longer to finish.
