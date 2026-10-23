# AnyMail — what changed in 0.1.12

Release clients now strip debug symbols and remap home, workspace, and cargo registry paths at build time, so shipped binaries no longer expose product symbol names or readable source/build paths. The Windows client stays unsigned by owner waiver; macOS stays signed and notarized.
