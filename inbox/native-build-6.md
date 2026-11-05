# Pending decision on the "Native FFI builds" PR: keep Dart's source build mode?

What I did: the 6-commit version of the shared reproducible native builds work is ready on branch `native-build-6` (`bb0a47f9dc1a237c0656e56e9c6280e2adb0c9c6`), in `~/repos/rust-payjoin-native-build-6`. Its PR description is `~/repos/native-build-6-PR-2023.md`. It still needs the human to force push it to `spacebear21:native-build`, the head of PR #2023 "Native FFI builds" (https://github.com/payjoin/rust-payjoin/pull/2023), and paste that description.

Open decision: whether the Dart package keeps a `build: source` mode beside the default prebuilt libraries. The human posted the question to ethicnology of Bull Bitcoin, the main Dart consumer: https://github.com/payjoin/rust-payjoin/pull/2023#issuecomment-6666602369

- Keep it if Bull needs to compile payjoin themselves, for example a "built from source" policy or a future F-Droid listing. F-Droid's inclusion policy only allows prebuilt dependency binaries from listed sources (Debian, trusted Maven repos, the Flutter SDK, PyPI wheels, Nix cache, Rust/Rustup and a few more), not pub.dev, and has no exception for reproducible ones. So a prebuilt-only package would very likely fail F-Droid review unless payjoin is compiled in the recipe.
- Drop it if Bull is fine with the attested prebuilt libraries. That removes the shipped Rust crate and lockfile, the lock staging in prepare-publish.sh, and the source reproducibility CI job, and makes Dart work like Python and C#. `build: workspace` stays for the repository itself.

What I need: ethicnology's answer, then the human's choice between keeping both modes and prebuilt only. If prebuilt only, I prepare that change on top of `native-build-6`. Nothing else is blocked; the branch is ready to push as it is.
