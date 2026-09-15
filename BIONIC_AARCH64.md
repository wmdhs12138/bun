# Bun for Android bionic aarch64

This fork carries a GitHub Actions build dedicated to a native Android/Termux
runtime. It uses Bun's upstream Android CI configuration rather than patching a
glibc executable after the fact.

## Build target

- architecture: AArch64
- libc and loader: Android bionic, `/system/bin/linker64`
- minimum Android API: 28 (Android 9)
- Android NDK: r27c
- build profile: upstream CI release build for `linux/aarch64/android`
- minimum source version: Bun 1.4.3

Run **Build Bun for Android bionic aarch64** from the Actions tab. A successful
run uploads `bun-linux-aarch64-android-<commit>.zip` plus its SHA-256 checksum as
a workflow artifact. The archive also contains the ELF inspection reports and
the exact source/toolchain revisions used by the run.

The default source commit is `5fce36ebb613a1fca77192c577a2c0a6a132e953`.
Claude Code 2.1.272 has been verified against an Android Bun built from that
revision. You can enter another Bun tag, branch, or commit when dispatching the
workflow, but each revision needs a new Android and Claude Code compatibility
test before promotion.

The workflow validates that the result is an AArch64 PIE, requests Android's
linker, contains Bun's standalone `.bun` section, and has no glibc loader or
`libc.so.6` dependency. GitHub's Linux runner cannot execute a bionic program;
runtime and Claude Code compatibility tests must therefore run on an Android
device before promoting an artifact.

The workflow intentionally uploads only a workflow artifact and does not
publish GitHub Releases automatically.

## Direct-exec Claude Code artifact

Enable the manual `build_claude` input to continue from the freshly compiled
Bun ELF, extract and patch the selected Claude Code standalone graph, embed the
Android compatibility prelude into its entry module, and graft it into Bun.
The second artifact is a self-contained `claude` ELF: it does not need an
external Bun executable, JavaScript preload, or `BUN_OPTIONS` to start.

The Claude artifact is an opt-in, one-day Actions artifact for personal testing
and is never attached to a GitHub Release. Claude Code is proprietary; do not
redistribute the resulting binary. The hosted runner performs structural graph
and ELF verification but cannot execute Android/Bionic binaries. Promote a new
Bun or Claude revision only after running `./claude` and the TUI smoke test on
an Android device.
