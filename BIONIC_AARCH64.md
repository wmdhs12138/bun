# Bun for Android bionic aarch64

This fork carries a GitHub Actions build dedicated to a native Android/Termux
runtime. It uses Bun's upstream `android-release` profile rather than patching a
glibc executable after the fact.

## Build target

- architecture: AArch64
- libc and loader: Android bionic, `/system/bin/linker64`
- minimum Android API: 28 (Android 9)
- Android NDK: r27c
- build profile: `android-release`
- minimum source version: Bun 1.4.3

Run **Build Bun for Android bionic aarch64** from the Actions tab. A successful
run uploads `bun-linux-aarch64-android-<commit>.zip` plus its SHA-256 checksum as
a workflow artifact. The archive also contains the ELF inspection reports and
the exact source/toolchain revisions used by the run.

The workflow validates that the result is an AArch64 PIE, requests Android's
linker, contains Bun's standalone `.bun` section, and has no glibc loader or
`libc.so.6` dependency. GitHub's Linux runner cannot execute a bionic program;
runtime and Claude Code compatibility tests must therefore run on an Android
device before promoting an artifact.

The workflow also runs for tags matching `bionic-v*`. It intentionally uploads
only a workflow artifact and does not publish GitHub Releases automatically.
