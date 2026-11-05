# Dart source build mode: answered, keep both modes

ethicnology answered on PR #2023 "Native FFI builds" (https://github.com/payjoin/rust-payjoin/pull/2023#issuecomment-6669901079): Bull Bitcoin can use the prebuilt libraries, but needs to build from source for a future F-Droid listing (their tracking issue https://github.com/SatoshiPortal/bullbitcoin-mobile/issues/297, open since 2024-05). So the Dart package keeps both `build: prebuilt` (default) and `build: source`.

That is what branch `native-build-6` (`bb0a47f9dc1a237c0656e56e9c6280e2adb0c9c6`, in `~/repos/rust-payjoin-native-build-6`) already does, so no code change is needed.

What I need: the human to force push `native-build-6` to `spacebear21:native-build`, the head of PR #2023, and replace the PR description with `~/repos/native-build-6-PR-2023.md`. Nothing else is pending.
