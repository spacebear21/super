# rust-payjoin

Main clone ~/repos/rust-payjoin, worktrees ~/repos/rust-payjoin-<slug>. Many parallel workers in Herdr workspace wS; most predate my memory.

## Native FFI builds: PR #2023 (formerly #2016)

#2016 (head native-build-contract) was closed 2026-10-30 as superseded by #2023 'Native FFI builds', open, base master, head spacebear21:native-build at 9c01ab79 (nine commits) as of 2026-11-05 02:20. The workers in wS still talked about #2016; corrected wS:pB at 02:20.

- Branch native-build at 2b47036c: nine commits, the PR as opened.
- Branch native-build-6 at bb0a47f9dc1a237c0656e56e9c6280e2adb0c9c6 (worktree ~/repos/rust-payjoin-native-build-6, on origin/master 14d0eb94): the same PR without three independent commits (16 KB pages on NDK r27, Rust 1.85.1 release bindings, Kotlin lock). Built by the phase2 worker (wS:pP). Report at ~/repos/native-build-6-REPORT.md: all checks pass except one Dart test that needs IPv6 loopback, unchanged from master; native hashes match native-build exactly when built from the same Rust sources. Nothing pushed.
- wS:pB's verdict (02:11): force push native-build-6 to spacebear21:native-build-contract, the head branch of #2016, then use the short PR description it gave the human earlier minus the sentence about the first three commits. native-build stays as the fallback. Corrected target: the force push goes to spacebear21:native-build (#2023). Brought to the human 02:21. wS:pB agreed (02:13 pane clock): the remote native-build at 9c01ab79 is only its nine-commit branch rebased onto master 2abd11ae with nothing else, so the force push loses nothing. PR description for #2023 ready at ~/repos/native-build-6-PR-2023.md.
