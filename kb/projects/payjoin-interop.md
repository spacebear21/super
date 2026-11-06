# Payjoin interoperability matrix (design, 2026-11-06)

What the human asked for: a matrix that ensures maximum compatibility between Payjoin implementations and stays current. Research behind this: four Token Factory agents (prior art, rust-payjoin reusable infrastructure, implementation drivability, scenario catalogue); the prior-art agent was stopped and its subagents' notes salvaged; the drivability survey was still running when this was written and gets folded in later. Design choices and their reasons live here; the code, when built, documents itself.

## What the prior art teaches

- **QUIC Interop Runner is the model that lasted.** Every client against every server, nightly, in Docker, with a tiny container contract (role, test case, a few environment variables, a shared directory for outputs) so the runner knows nothing about an implementation's internals. Results are a JSON file and a static page. It verifies outcomes itself from captures and transferred bytes instead of trusting a participant's exit code. Weak spots to avoid: result provenance (no commit or image digest per cell), "unsupported" conflated with "failed", and missing jobs shown as empty cells.
- **Lightning never got a standing matrix.** Events (2016, 2017, 2018) produced specs and, once, a reusable three-implementation integration suite; the 2023 interop initiative stalled before artifacts; today only LDK Node CI tests a few fixed pairings. Lesson: an event matrix does not become continuous QA by itself; only a suite someone runs nightly does.
- **Matrix's Complement and SyTest** can mix server images but track one implementation per run and test moving branch heads, so a green run is tied to whatever commits resolved that day and there is no release-pinned compatibility record.
- **Nostr** has single-relay auditors and hand-maintained compatibility tables; every broader matrix attempt was archived.
- **IETF hackathon matrices** are pairwise result codes from one room on one day, with no builds or provenance recorded.

Copy: the minimal container contract; nightly runs against pinned releases with provenance per cell; harness-side verification from the chain and the wire; distinct result states (pass, fail, unsupported as declared by the driver, not run, infrastructure error); a published page whose main view is the diff against the previous run. Avoid: moving branch heads as the only target, per-implementation test interfaces, relying on events, and a participant's own success message as the verdict.

## Shape

- **Sheets by protocol path.** v1 sender with v1 receiver; v2 sender with v2 receiver through a directory and relay; v2 sender falling back to a v1 receiver; v1 sender reaching a v2 receiver through the directory's v1 compatibility. Rows are senders at a version, columns receivers at a version. For the v2 sheets a third axis is the directory and relay implementation; today that is only payjoin-mailroom, so it is a column group, not a dimension.
- **A conformance column needing no peer:** URI and PSBT vectors (BIP78 vectors already embedded in payjoin-test-utils; BIP77 fragment fixtures; parser edge cases) run against each implementation's parser alone. This covers the URI-only libraries (bitcoin_uri, bip21 ports, lwk, waila, unified-uri) which are otherwise not matrix participants.
- **Star first, mesh second.** Every implementation is first run against the reference pair in both roles, which yields a full row and column per implementation quickly. Drivable implementations then also run pairwise against each other nightly; that is where two non-reference implementations disagree.
- **Each cell is a set of scenario results, with provenance:** implementation commit or tag, image digest, harness commit, reference version, date. Never only a colour.

## Reference pair

payjoin-cli from rust-payjoin, built with all features (v1 and v2 and local TLS), as reference sender and receiver; payjoin-mailroom as reference directory and relay. Reason: it is the only implementation that speaks every role of both BIPs, its own e2e tests already encode the unattended regtest recipe for both protocol paths, and most other implementations embed it, so a disagreement with the reference is a disagreement with the code they themselves depend on, at a different version. Version skew is itself a sheet: payjoin at the versions the wallets pin (0.23 for Cake until its native-bindings PR lands, 1.0.0 for Bull and SatoshiPortal, 0.25 for bdk-cli) against current.

## Scenarios

Agent D's catalogue: 60 scenarios across the four paths plus directory and relay variants, each with the BIP clause, what the test does, a pass criterion, and a priority. Pass for a success case is both parties agreeing on the final raw transaction, txid, outputs and fee, confirmed by the harness from the regtest node; for a rejection case no invalid proposal signed or reported as success, with the original broadcast exactly once where fallback is valid. Scenarios are grouped as: wave 1, the happy paths and fallbacks (basic exchange, mixed and taproot inputs, insertion positions, fee contribution, fallback on failure; v2 basic URI, encryption round trip, offline delivery and resume, expiry, settlement choice; v1 detection through the directory, timeout fallback, v2 sender to v1 receiver), about 15; wave 2, the remaining "must" rows (pjos and substitution boundaries, fee bounds, size ceilings, OHTTP construction, mailbox statuses); wave 3, "should" and "nice" (encoding round trips, CORS, legacy fragment order, relay federation). Several wave 1 and 2 scenarios correspond to drafted audit findings (pjos dropped, req-pj ignored, premature fallback, unbounded bodies): the audit reads code, the matrix runs it, and a matrix failure is the strongest evidence a report can carry.

## Driver contract

Two layers. The floor, which any implementation can meet in a day, is QUIC-style: a container (or a local command for the first wave) that takes a role, a scenario name, regtest RPC credentials, the directory and relay URLs and the trusting certificate, and for the sender the URI; the receiver writes its URI to a well-known file; both write a result file with a structured state (waiting, proposal posted, payjoin signed, payjoin broadcast, fallback broadcast, rejected, expired, unsupported, transport failure) and the PSBTs and txids they saw. The harness decides pass or fail from the node and from a wire capture at the relay, not from that file alone. The ceiling, which the reference implements and others grow into, is agent D's six-command contract: create receiver session, send to URI, drive one step (poll, process one message, pause, restore, broadcast), report outcome, report broadcast state, read protocol trace. The step command is what makes timing scenarios (offline delivery, expiry races, restart continuity) deterministic; without it those scenarios run with wall-clock waits and are marked as such. Malformed peer messages come from a harness-side mutating proxy, so no wallet has to implement misbehaviour.

## What rust-payjoin already gives us (agent B)

- payjoin-test-utils: regtest bitcoind with funded sender and receiver wallets, TestServices starting one HTTPS directory and two OHTTP relays with a self-signed certificate and health polling, the BIP78 vectors and BIP77 URI fixtures, deterministic OHTTP keys. A library, not a service: the harness needs a small Rust launcher that exports URLs, certificate, keys and RPC as a manifest for non-Rust participants.
- payjoin-cli e2e tests: the two shortest matrix rows already exist as tests (v2 with offline resume and confirmed completion; v1 with a live local HTTPS receiver), including subprocess handling, stdout markers and cleanup. Outcomes are text markers, so a machine-readable outcome flag is the first upstream contribution to ask for.
- FFI bindings in Python, JavaScript, Dart, C# and Kotlin all have headless regtest integration tests; the C# walkthrough is the clearest language-neutral description of driving both state machines. Limit: the FFI SenderBuilder rejects BIP78 URIs, so bindings cannot be v1 senders today, which also means Bull, Cake and SatoshiPortal cannot either.
- Gaps: no standalone launcher, no JSON results, no cross-implementation or cross-version CI, no third-party adapters, mailroom needs separate directory and relay processes (self-loop guard) and has no self-signed TLS flag outside the Rust test API.

## Participants and waves

Drivability from the registry notes; the C survey refines this.

- **Wave 1 (CLI or API exists):** payjoin-cli (reference, both versions); bdk-cli (payjoin 0.25, v1 and v2, CLI); SatoshiPortal cypherapp (v2 sender and receiver behind a JSON-RPC API, Node, Docker); BTCPay Server with its BIP78 library (v1 receiver and sender, the largest user base; BTCPay ships regtest Docker; the BIP78.Sender NuGet package also works as a standalone v1 sender adapter); the BTCPay async plugin (v2 receiver, critical infrastructure for payjoin.org/donate, runs inside the same BTCPay container); bitcoinjs-payjoin-client (v1 sender library, a small Node adapter, and the same adapter pinned at v1.0.1 stands in for BlueWallet's vendored copy); the five FFI bindings at the versions wallets pin, as stand-ins for Bull, Cake and SatoshiPortal cores.
- **Wave 2 (adapter or headless entry point needed):** Sparrow (v1 sender, hand-written; it has a terminal mode worth checking), GingerWallet (v1 sender, C#), Phobia (v1 sender, C#), Unify (v1 both, Swift, needs a Core node anyway), JoinMarket via its scripts (v1 both; the receiver wants a Tor hidden service, so a no-Tor regtest path is the question), bark (v2 receiver for Ark boards, GitLab).
- **Wave 3 (real wallet drivers):** Bull mobile and Cake with their own URI parsing and policy layers, which the bindings stand-in does not cover (req-pj, pjos handling and interactive-receiver assumptions are exactly where the audit found their bugs); hardware wallets (Coldcard, Trezor) as signers inside a reference sender through their emulators; BitMask after its payment.rs rewrite lands.
- **Conformance only:** the URI and PSBT libraries.

## Keeping it current

- Nightly, after the audit, on the workhorse to start (the audit's cron slot and notify helper exist; add a second schedule entry). Participants are pinned to their latest release; a second row per participant tracks its main branch so a regression shows before it ships. The audit registry already discovers new implementations and new commits every night; a new active implementation or a new release becomes a matrix candidate automatically, and the matrix's result page links the audit's findings for the pair.
- Results: a JSON file per run, a static page with the sheets, per-cell history and the diff against the previous run; the run's diff goes to the super inbox like the audit's summary. A new failure opens a draft issue carrying the scenario transcript: URI, both PSBTs, the wire trace, the two versions.
- Images: built by us in the matrix repo first (Dockerfiles per participant pinned to a tag), moving to implementation-maintained images once maintainers want to own them, as QUIC did.

## Where it lives and what it costs

A new repository (proposed: payjoin/payjoin-interop, runner in Rust so it can link payjoin-test-utils directly, participants as Docker images, results as static files). Two small upstream contributions make everything simpler and belong in rust-payjoin regardless: a test-utils launcher binary that exports the environment manifest, and a machine-readable outcome from payjoin-cli. Wave 1 with the 15 happy-path scenarios against the reference is a few weeks of one worker; the nightly run is tens of minutes at regtest speed. The scenario catalogue, the driver contract and the reuse inventory are in this session's scratchpad until the repo exists; copy them into its docs as the first commit.

## Decisions wanted from the human

1. The repository: name and organisation (payjoin/payjoin-interop under the payjoin org, or private first).
2. The reference pair as proposed (payjoin-cli plus mailroom).
3. Wave 1 as proposed, and whether BTCPay's own Docker regtest stack is acceptable as the way to run the two BTCPay participants.
4. Whether to open the two upstream asks on rust-payjoin now (launcher binary, machine-readable outcome) or build them in the matrix repo first.
5. Whether cross-version skew (payjoin 0.23 and 1.0.0 against 1.2.0) is wave 1, given Cake and Bull pin old versions.
