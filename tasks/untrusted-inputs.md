# witness_utxo fee issue: team brief and path forward

## What it is
A receiver can inflate the claimed amount of an input it adds to a payjoin; every sender fee check reads that claim, so the receiver diverts the sender's intended network fee into its own output. The take per transaction is the sender's original fee plus the additional fee contribution, less the relay minimum. Medium, fee bounded, under a private advisory.

## Where it stands
- The first private fix (require the previous transaction on every receiver input) was rejected by you on 2026-11-05: it breaks Bull Bitcoin and Cake receivers as shipped.
- A rethink ran on 2026-11-05 (one agent from fundamentals, a fleet surveying Lightning, Core, BDK, Ledger, Coldcard, Wasabi, Liquid). Signature verification of the receiver's input was shown unsound; requiring the previous transaction is the only sound binding for segwit v0 and nobody has it today.
- You chose the path on 2026-11-06: ship the sound hardening that breaks nobody, accept and document the residual, add the FFI rail, adopt the Coin design for 2.0, keep a by-txid sender fetch as a later opt in.
- The team brief is written and the fee cap survey of every shipping sender is folded in as its section 6.

## What is asked
1. Read `~/review-drafts/untrusted-inputs/team-brief.md` and take it to the private advisory thread to open the team discussion. It is pasteable into GitHub.
2. Ask Bull whether their exchange sending wallet signs taproot or segwit v0. It decides whether the most important deployment gets the self destructing taproot signature for free.
3. Say whether the advisory is published with the residual acknowledged or held.

## Pointers
Softened 2.0 report: `~/research/payjoin-2.0-inputs/REPORT-2026-11-06.md`. Rethink and surveys: `~/review-drafts/untrusted-inputs/`. The rejected fix: worktree `~/repos/rust-payjoin-verify-receiver-inputs`. Knowledge base: `kb/topics/witness-utxo-fee-issue.md`.
