# rust-payjoin

Main clone ~/repos/rust-payjoin, worktrees ~/repos/rust-payjoin-<slug>, workers in Herdr workspace wS. The repo and its PRs document what was built; this file holds why, what was argued out of band, and what was abandoned.

## Standing directions from the human

- "simplification is the name of the game. i don't want any overkill scripts and especially tests in this PR." Hyper specific tests for esoteric edge cases that a byte for byte comparison would catch are unwelcome.
- Commit history should read "as if this was the plan from the start"; "we don't need 20 commits here". Few commits, each passing CI on its own, none referencing something that only exists later. Mechanical changes apart from behavioural ones.
- PR descriptions and comments very short ("way too long", "much more succinct").
- Questions about the code go to the human, not to other maintainers ("don't ask dan gould, ask me").
- Many reproducibility jobs on every PR are not acceptable; they belong in release workflows or go.
- Everything ships through PRs the human pushes; nothing is opened on their behalf.
- The API must not take on wallet responsibilities. No "hacky workaround going into persistent storage". New public surface has to be right the first time: "no room for 'renaming it later'". Challenge exposed types that only the library uses internally.
- Prefer non-breaking changes; a security fix is small, additive and backportable; no attack details in anything public while a fix is private; do not make payjoins harder "for no good reason"; stay on the stated problem.
- "no need to validate all bindings, let's leave that for CI". A colleague's tests go in a separate commit for independent review.
- If a plan proves wrong, stop and write down why instead of improvising a different design. Do not modify the contract branch; write a question instead. Keep the maintainer's commit wording.

## Native FFI builds (the work behind PRs 1996, 2016 and 2023)

- **Why prebuilt libraries for every language instead of Dart building from source.** The human asked whether shipping prebuilt library files everywhere, as C# already does, beats the source build that PR 1996 and the older 1815 pursued. Agreed yes. Named costs: F-Droid style distributors reject prebuilt binaries, iOS needs macOS runners, feature flags change the bytes, Python's auditwheel rewrites the shared object. 1996's lock and path remap work survives as the source build fallback and as a cross check of the prebuilt files. The human: "why merge 1996 if the better architecture would make it not matter?" Three phases followed: a small PR to unblock Bull Bitcoin so they can drop their fork, one native build contract across all bindings, then build once reproducibly and ship the same files in every package. The last two merged into one PR because stacked PRs cannot use a fork branch as base on the upstream repo.
- **Dart binaries are embedded, not downloaded at build time.** All eleven libraries compress to about 28 MB against pub.dev's 100 MiB limit; embedding removes the build time dependency on GitHub, keeps builds hermetic and offline, and needs one approval. Integrity rests on pub's archive hash and the attestation. The cost, every consumer downloads all eleven, is "the trade the maintainer chose". The downloading hook survives only as a local branch.
- **The Dart only prebuilt mechanism (PR 1307) stays closed**: it would not help Bull Bitcoin, who disable it, and it cuts against one shared artifact set. Only its auto, always and never modes were worth keeping.
- **Publishing**: Dart alongside the other registries with one approval. payjoin-ffi and payjoin-test-utils stay unpublished because that needs the git dependencies removed and the Rust API kept stable.
- **No native builds on PRs**; contract changes are validated by the daily cron first. Release gating only checks that the PR CI reproducibility jobs passed, called temporary scaffolding.
- **macOS floor lowered to Flutter's** (10.15 x86_64, 11.0 arm64, iOS 13) in place of a deployment target check that was removed "as if it was never there".
- **Three unrelated commits** (16 KB pages on NDK r27, Rust 1.85.1 devshell moves, Kotlin lock) were separated so they can be their own small PRs; a trimmed branch without them exists for comparison and changes no library bytes. Whether the Rust move merges after the main PR is open.
- **The Dart package keeps both build modes: `prebuilt` (default) and `source`** (settled 2026-11-05). Bull Bitcoin's Dart maintainer answered on PR 2023: the prebuilt libraries work for them today, but they need to build payjoin from source for a future F-Droid listing (their tracking issue has been open since May 2024). That matches the F-Droid reasoning already weighed here: F-Droid's inclusion policy allows prebuilt dependency binaries only from listed sources such as Debian, trusted Maven, the Flutter SDK, PyPI wheels, Nix and Rust toolchains, not pub.dev, with no exception for reproducible ones. The alternative, prebuilt only (dropping the shipped crate and lockfile, the lock staging and the source reproducibility CI job, so Dart works like Python and C#), was therefore not taken.
- **Out of scope by choice**: reproducible archives (wheel, nupkg); a known gap for C#'s dll, with deterministic C# builds and report only archive checks proposed. Left for later: splitting packaging from testing so a native failure does not block test jobs, pruning stale assets on a release re-run, an environment on the release job, running the real hook in the iOS simulator check, a real Flutter app build in prebuilt mode, NDK and zigbuild caching.
- **Lessons**: Cargo does not rebuild dependencies when the deployment target changes, so hashes from a stale target directory lie; verifiers build from a clean target. Leftover bitcoind processes hold the test output pipe and hang the waiters. One Dart test needs IPv6 loopback, which this box lacks. A Rust 1.85.1 move broke the Dart libz job, so Dart stayed on 1.85.0. cargo-zigbuild refuses iOS targets; zig can be called directly.

## Monitor, settlement and what fallback_tx is exposed on (PR 1971 review, with the 1994 question)

The conversation that kept fallback_tx on SessionHistory in PR 1994 is not in any transcript on this box; the closest reasoning is from the review of 1971 by the same worker.

- The human was "especially unconvinced about exposing fallback_tx on the monitor (and other) typestates", and asked whether a fallback txid on the trait made sense when SessionHistory already has fallback_tx, "unless we were to expose classify() on the trait".
- The reasoning for keeping fallback_tx on SessionHistory only, and off the receiver states: recognising the fallback needs no transaction bytes, and bytes on every state invite a broadcast the session never records; after cut-through the original transaction is the wrong thing to broadcast; a txid lookup misses a re-signed or malleated fallback where a prevout lookup catches it; wallets find the fallback anyway because it pays their address; SessionHistory's fallback_tx remains the one shortcut (its txid is one call away) with no new surface. A fallback txid accessor alone was rejected as it misses malleation. The human did not reply on this point in that session.
- A persisted settlement verdict was rejected: the log is permanent and the canonical transaction can change. Classification lives both on the receiver (always current, cannot classify after close) and on SessionHistory (covers ended sessions, a snapshot).
- The Resolution wrapper was questioned as undiscussed public API; the human "decided to keep the wrapper". Exhaustive public enums are a semver trap, so non_exhaustive was advised; the classification accessor stays because it is the only way a wallet learns which transaction settled. A rename away from the _tx abbreviation was dropped for consistency.
- Rejected: a bare close (leaves a signed proposal live), double spending receiver prevouts to give up (costly, receiver unpaid), an opaque Resolution, predicates instead of the enum. On LDK research: the wallet, not the session log, owns observation state.

## BIP-78 witness_utxo fee gap

See [the topic page](../topics/witness-utxo-fee-issue.md): conclusion, the human's words, routes tried, what is open with the team, where the evidence is.

## Standing directions from the human on rust-payjoin (from the same session)

- No attack details in anything public facing while a fix is private.
- Prefer non-breaking changes; keep a security minor release small, additive and backportable.
- Do not make payjoins harder "for no good reason".
- Stay on the stated problem; scope creep goes to a later phase.
- "no need to validate all bindings, let's leave that for CI".
- A colleague's tests go in a separate commit for independent review.

## Security audit of BIP78/BIP77 (OpenCode session, 2026-11)

- Conclusion: no funds loss in the core except the witness_utxo fee margin theft above, which that audit found independently. It judged a receiver side signature check the better fix because it proves the claimed value, while the advisory work settled on requiring non_witness_utxo; the open trade off either way is that witness only P2WSH, taproot script path and legacy receiver inputs get rejected, a sender visible change. There is no pure Rust script verifier in rust-bitcoin (its built in one needs the C library and a complete transaction).
- Directions from the human during it: "vulnerabilities that involve either party constructing an unbroadcastable transaction are irrelevant, as the fallback transaction may always be broadcasted instead"; sender and receiver are mutually untrusted; do not rehash known issues; repeat the scan with every abliterated Token Factory model and consolidate; distrust hand written signature code, "it's doing a lot of sensitive work that i'd hate to get wrong".
- Lessons about running it: OpenCode sub-agents hung on permission dialogs without the worker noticing ("nothings' happened in the last 15 minutes"); a global permissive opencode config fixed it. Adversarial harness failures were the harness's own signing order bugs. The cheapest model found operational issues the strong ones missed.

## Bull Bitcoin runs payjoin in production on both sides (blog post of 2026-11-04, shared by the human)

Bull Bitcoin's exchange does payjoin by default for on-chain buys and sells through BULL Wallet (minimum 25,000 sats, Lightning and Liquid excluded, falls back to a plain transaction on failure, first purchase into an empty wallet is not a payjoin). In a buy the user's wallet is the receiver and the exchange is the sender; in a sell the exchange is the receiver adding one UTXO. Exchange side code is github.com/SatoshiPortal/payjoin, wallet side SatoshiPortal/bullbitcoin-mobile. Why it matters here: Bull is at once the honest receiver no fix may break and a production sender facing many untrusted receivers, where a per transaction fee budget theft repeats once per trade. The post makes no technical claims about input validation and asks nothing of the project.
