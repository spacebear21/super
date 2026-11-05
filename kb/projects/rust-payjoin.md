# rust-payjoin

Main clone ~/repos/rust-payjoin, worktrees ~/repos/rust-payjoin-<slug>. Many parallel workers in Herdr workspace wS; most predate my memory.

## Native FFI builds: PR #2023 (formerly #2016)

#2016 (head native-build-contract) was closed 2026-10-30 as superseded by #2023 'Native FFI builds', open, base master, head spacebear21:native-build at 9c01ab79 (nine commits) as of 2026-11-05 02:20. The workers in wS still talked about #2016; corrected wS:pB at 02:20.

- Branch native-build at 2b47036c: nine commits, the PR as opened.
- Branch native-build-6 at bb0a47f9dc1a237c0656e56e9c6280e2adb0c9c6 (worktree ~/repos/rust-payjoin-native-build-6, on origin/master 14d0eb94): the same PR without three independent commits (16 KB pages on NDK r27, Rust 1.85.1 release bindings, Kotlin lock). Built by the phase2 worker (wS:pP). Report at ~/repos/native-build-6-REPORT.md: all checks pass except one Dart test that needs IPv6 loopback, unchanged from master; native hashes match native-build exactly when built from the same Rust sources. Nothing pushed.
- wS:pB's verdict (02:11): force push native-build-6 to spacebear21:native-build-contract, the head branch of #2016, then use the short PR description it gave the human earlier minus the sentence about the first three commits. native-build stays as the fallback. Corrected target: the force push goes to spacebear21:native-build (#2023). Brought to the human 02:21. wS:pB agreed (02:13 pane clock): the remote native-build at 9c01ab79 is only its nine-commit branch rebased onto master 2abd11ae with nothing else, so the force push loses nothing. PR description for #2023 ready at ~/repos/native-build-6-PR-2023.md.

## BIP-78 witness_utxo fee gap (decision context recovered from the worker's transcript, 2026-11-05)

The problem: a receiver can inflate the witness_utxo amount on the input it adds, the sender's fee checks trust that amount, and the receiver keeps the difference, bounded by the sender's fee budget. Rated medium. Cake Wallet is exposed even for taproot senders because it adds no checks of its own.

- **Handled as a private security advisory, not a public PR.** Docs, commit messages, test names and comments must not explain the attack. Codex refused to do a security review twice, so it reviewed code quality only; a Claude agent reviewed the logic.
- **The rule that won**: the sender requires a txid-checked non_witness_utxo on every receiver input, unless the sender itself spends a taproot key-path input signed DEFAULT or ALL. It replaced an earlier signature-verification scheme after a colleague showed P2WSH disguised as P2WPKH still passed. The human: "would it not be simpler to just always require non_witness_utxo and do away with all this nuance?" Hard-rejecting witness_utxo-only inputs had first been rejected over interop, then adopted.
- **Rejected**: forcing a 2.0 ("the PR shouldn't already be marked as Breaking because it doesn't change the API"; breaking the FFI is fine while it is pre-1.0); a separate sender signature module (merged into the psbt module instead); a sender opt-out flag; a colleague's "contribution unchanged" exemption (shown insufficient); a script interpreter dependency; an "unspendable transaction" finding ("making an unspendable transaction is not a real vulnerability... it's a lose-lose").
- **Accepted gap**: receivers whose signers never fill non_witness_utxo. Docs point at Core and BDK. Under the rule, non-taproot senders reject both Bull and Cake receivers today and fall back to the original transaction, no funds lost, but the human said "lost payjoins aren't really acceptable at this point". The recommended answer, not implemented: the sender fetches previous transactions itself through an in-memory, additive proposal step with nothing newly persisted; a persisted typestate waits for 2.0. The BIP77 message size cap (7168 bytes) limits how large a previous transaction can travel.
- **Deferred**: the mirror issue on the receiver side (follow-up), from_prev_tx (scope creep, later phase), PSBTv2 (out of scope). A 2.0 Coin/ConfirmedUtxo design report exists in the worker's output.
- **The FFI gap had to be fixed in the same PR** (human).
- Blocker never worked around: fetching the private advisory fork returned 403/404.

## Standing directions from the human on rust-payjoin (from the same session)

- No attack details in anything public facing while a fix is private.
- Prefer non-breaking changes; keep a security minor release small, additive and backportable.
- Do not make payjoins harder "for no good reason".
- Stay on the stated problem; scope creep goes to a later phase.
- "no need to validate all bindings, let's leave that for CI".
- A colleague's tests go in a separate commit for independent review.
