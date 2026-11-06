# The witness_utxo fee issue (rust-payjoin sender, private advisory)

Status 2026-11-06: path decided by the human, team brief written, discussion with the team pending. Embargoed: nothing public names the attack while the fix is private.

## Conclusion

A receiver can inflate the claimed amount of an input it adds; every sender fee check reads that claim, so the receiver diverts the sender's intended network fee into an output of its own. The take is the sender's original fee plus max_additional_fee_contribution, less the relay minimum. [checked: fee survey folded into the brief, verified against check_fees by the synthesis agent, 2026-11-06] Severity medium, fee bounded; the sender's out of pocket stays what it authorized. The decided path: ship the sound hardening that breaks no receiver (authenticate non_witness_utxo against the txid, fees from authenticated previous outputs, reject contradicting UTXO fields), accept and document the bare witness_utxo segwit v0 residual, add the additive FFI rail, adopt the Coin design as the 2.0 receiver API, keep a by-txid sender fetch as a later opt in. [checked: human, 2026-11-06, "go forward with that"]

## What the human said

- 2026-11-05: the private PR requiring the previous transaction on every receiver input is rejected because it breaks honest Bull Bitcoin and Cake receivers as shipped, "just unacceptable especially considering the severity of the issue is not that high". Rethink from fundamentals. [checked: super transcript 2026-11-05 17:38] "lost payjoins aren't really acceptable at this point." [unconfirmed: in no transcript on this box]
- 2026-11-05: "would it not be simpler to just always require non_witness_utxo and do away with all this nuance?" (said while the strict rule was being designed; superseded by the rejection above). [unconfirmed: in no transcript on this box]
- "the PR shouldn't already be marked as Breaking because it doesn't change the API"; breaking the FFI is fine while it is pre-1.0; the FFI gap had to be fixed in the same PR.
- "making an unspendable transaction is not a real vulnerability... it's a lose-lose"; an unbroadcastable transaction is never a finding since the fallback can always be broadcast.
- 2026-11-06: "go forward with that. i'd like to start a discussion with the rest of team about this, so i want a full brief with proposed path forward." [checked: super transcript 2026-11-06 22:13]
- Standing: no attack details in anything public while the fix is private; prefer non-breaking changes; a security minor small, additive and backportable; do not make payjoins harder "for no good reason"; interop weighs more than closing a medium quickly; Bull's exchange payjoin is the most important deployment.

## Routes tried and why they failed

- Require the previous transaction on every receiver input: sound, breaks Bull and Cake as shipped (both build inputs with a bare witness_utxo; Cake's finalizer strips non_witness_utxo). Ledger required it in 2020, broke Electrum, BTCPay and Wasabi, reversed in 2022. [checked: rejected PR branch, downstream audits, hardware wallet survey]
- Verify the receiver's own signature against the claimed witness_utxo: unsound. A receiver grinds a pubkey whose bytes are also a P2WSH script that drops the signature, funds a P2WSH coin, claims it as P2WPKH with an inflated amount; the sender verifies a signature consensus never runs. Executed on the consensus verifier. [checked: synthesis report]
- Rely on the sender's taproot signature: real, but whether it binds the claimed or the real amount is a property of the wallet's signer (BDK and bitcoind sign over the claim, Cake fetches the truth from Electrum), and no field sender spends taproot today. At most an opt in. [checked: experiment-taproot-amount]
- Chain lookup by the sender: sound, new API, adopters only; an outpoint amount lookup leaks the receiver's coins to the sender's indexer, a by txid fetch of a missing previous transaction does not. Deferred, by txid preferred. [checked: synthesis, brief]

## Open with the team

Whether Bull's exchange sending wallet signs taproot or segwit v0 (deployment config; decides whether the most important deployment gets the self destructing taproot signature for free). When Cake and Bull can ship previous transactions receiver side. Publish the advisory with the residual acknowledged (the writer's recommendation) or hold. Per deployment at 10 sat/vB on a 141 vB send: most senders about 1,900 sats, BTCPay about 2,300, zero contribution senders about 1,200, the Bull exchange about 1,200 on a small withdrawal and far more on a consolidation, bitmask and Trezor blocked. [checked: fee survey]

## Where the evidence is

- Team brief and softened 2.0 report: ~/review-drafts/untrusted-inputs/team-brief.md, ~/research/payjoin-2.0-inputs/REPORT-2026-11-06.md (original REPORT.md untouched beside it, with ldk, bull, cake, hww and sender-sm sub-reports).
- Rethink reports: ~/review-drafts/untrusted-inputs/ (synthesis, fundamentals, ten discovery reports, experiment-taproot-amount), fee survey under fee-survey/ (fee-a, fee-b, fee-c). [checked: ls 2026-11-06]
- The rejected private fix: worktree ~/repos/rust-payjoin-verify-receiver-inputs, two commits (validate UTXO fields, require non_witness_utxo); the first is the hardening that ships.
- The Coin / ConfirmedUtxo design, raised by the human on 2026-11-06 ("what about the `Coin`/`ConfirmedUtxo` design that iirc LDK uses to solve the untrusted witness_utxo issues? like for a 2.0? did any of the survey agents consider this?") [checked: super transcript 2026-11-06 22:02]: receiver side, modelled on LDK; makes future receivers send the previous transaction by construction; cannot protect a sender against today's receivers. The rethink set the InputPair reshape aside as off the sender's critical path; InputPair is serialized into the v2 session JSON, so enlarging it has a durable migration cost, which favours the FFI record rail first. [checked: 2.0 report, synthesis addendum]
- Blocker never worked around: this box's read-only token gets 403 on the private advisory fork, so the advisory's own branch was never fetched here. [checked: fee fix worker transcript, session ab359b69]
