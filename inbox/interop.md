# interop: first checkpoint, the reference pair runs end to end

Branch `interop` in the worktree `~/repos/payjoin-audit-interop`, commit `e5189b2` (six commits on top of master `bc6bd9b`). Nothing is needed from outside the machine; I am continuing with deliverables 5 to 7. This note is the checkpoint the brief asked for.

## Done

- The move of home is complete: the design and research live under `docs/interop/`, the design's "where it lives" section now says why it lives here, and `~/repos/payjoin-interop` is deleted (it had one commit, never pushed).
- `interop/` is a Cargo workspace with the runner. `interop env` starts regtest Core (with txindex), funded sender and receiver wallets, one HTTPS directory and two relays behind one self-signed certificate, exports the JSON manifest, health-polls, and tears down on Ctrl-C. It links payjoin-test-utils, payjoin and payjoin-mailroom at the payjoin-cli-1.0.0 commit.
- The driver contract is in `docs/interop/contract.md`: a participant directory with a manifest naming its registry project id, a ref, build and run commands, and declared unsupported combinations; the run command takes role, action (start, resume, cancel) and scenario id; results come back as `result.json` with the design's states. Participants with their own wallet ask the runner for coins through a file so the runner still knows whose outpoints are whose.
- The verdict reads the node (every transaction in the mempool or mined during the flow, inputs mapped to the party the environment funded) and uses the participants' reports only as witnesses that must agree. It is pure and unit tested without a network.
- Scenarios are data files under `interop/scenarios/`, one per catalogue id, with a flow of steps the runner interprets. Wave 1 has fourteen: A01 A08 A09 A16 A18 A25, B01 B09 B17 B18 B19, C01 C05 C08.
- The reference participant `interop/participants/rust-payjoin/` builds payjoin-cli 1.0.0 with all features (2 min 43 s cold) and a Python adapter mirrors `payjoin-cli/tests/e2e.rs`.

## What runs and how long

`interop cell --sender rust-payjoin --receiver rust-payjoin --scenario <id>` runs one cell by hand. Each cell starts a fresh node and services (about three seconds) and the protocol itself takes one to five seconds; the expiry and timeout scenarios wait a few seconds more. All fourteen with the reference pair take under three minutes.

Results with the reference as both sender and receiver:

| outcome | scenarios |
| --- | --- |
| pass | A01, A25, B01, B17, B18, B19, C01, C05, C08 |
| unsupported (declared) | A16, A18 |
| fail | A08, A09, B09 |

## Things worth your eyes, no decision blocked on them

1. **Two payjoin-cli 1.0.0 crashes the matrix found.** With a bech32m (taproot) Core wallet the receiver panics at `payjoin-cli/src/app/wallet.rs:234` with `PsbtInputError(WeightError(NotSupported))` when it tries to contribute an input, so A08 and A09 fail. With a p2sh-segwit (nested) Core wallet the sender panics at the same line with `WeightError(NoRedeemScript)`, so B09 fails. These are the reference's own cells, so every taproot or nested SegWit cell in the matrix inherits them until payjoin-cli fixes it. They look like audit material (the matrix is the strongest evidence a report can carry); I have not filed anything.
2. **A16 and A18 are `unsupported` for payjoin-cli as BIP 78 sender, by declaration.** A successful BIP 78 send prints neither the original PSBT nor the fallback hex, so the harness has no baseline for the order and fee checks. The BIP 77 receiver does print the original it received and the verdict accepts that copy, so the same checks work on v2 paths. This is the design's first upstream ask (a machine-readable outcome from payjoin-cli) showing up as a hole in the matrix, which is what the design said would happen.
3. **Three flows are shaped by the floor contract.** With the receiver online, a payjoin-cli exchange completes in under half a second, faster than the runner can stop a process. So B19 (settlement choice) keeps the receiver offline while the original is posted, C05 resumes the receiver for a fixed ten seconds, and B09 runs online with a nested SegWit sender rather than a mid-flow restart (B17 covers the restart). The scenario notes say so. The step command of the ceiling contract would remove the wall-clock shape.
4. **Core download.** corepc-node downloads Bitcoin Core 29.0 at build time; `BITCOIND_DOWNLOAD_DIR` will point it at the audit cache in `bin/run-interop.sh` so the binary lives under `~/.cache/payjoin-audit/interop/` as asked.

## Next

Deliverable 5 (bdk-cli, payjoin-cli at the payjoin 0.23, 0.25 and 1.0.0 tags, bitcoinjs through a Node adapter on Core RPC since its own regtest pattern needs a Docker esplora), then results and page, `bin/run-interop.sh`, lint and README. I will write here again at the end.
