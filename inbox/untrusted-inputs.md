# Hand-off: fee survey folded into the team brief and softened report

Branch: `untrusted-inputs-design` (review drafts, no repo commits).

Updated in place: `~/review-drafts/untrusted-inputs/team-brief.md` (new section 6, the per deployment fee table and severity answer; the bound corrected in section 1, phase 1 and the advisory wording section; the three fee surveys added to the appendix) and `~/research/payjoin-2.0-inputs/REPORT-2026-11-06.md` (bound corrected in the summary, phase 1 and the downstream impact). The original `REPORT.md` is untouched.

## What I settled on the bound

fee-c is right, and I verified it against `check_fees` and `check_outputs` in `~/repos/rust-payjoin` at `64a81279`. Every fee check runs on the receiver's claimed `witness_utxo` values (`proposal.fee()` and the min fee rate check), while the only real value constraints are consensus and relay. So a receiver can inflate a bare `witness_utxo`, keep the claimed fee at or above the original, drive the real fee down to the relay minimum, and absorb the difference into an output of its own. The amount it can divert is the sender's original fee plus `max_additional_fee_contribution`, less the relay minimum, not `max_additional_fee_contribution` alone. I changed the wording everywhere it appeared, advisory section included.

Two nuances I added that the surveys did not state cleanly. First, the sender's own out of pocket is still bounded by what it authorized; the harm is that its intended network fee is redirected to the attacker and the broadcast transaction underpays, so it stays fee bounded theft, medium, but the absolute number is the original fee plus a contribution, which for a consolidation sender is large. Second, fee-c claimed only legacy receiver inputs work and segwit v0 is blocked because the receiver's signature commits to the amount. That is wrong for the shipped sender, which never verifies the receiver's signature against the claimed amount: the receiver signs its segwit v0 input honestly over the real amount and lies only in the `witness_utxo` metadata, so segwit v0, the common Cake and Bull case, is exploitable. I corrected this in both documents.

## What I settled on BTCPay

fee-b is right, fee-c is wrong. I read the actual `btcpayserver-bip78` code at `df3ac21` that BTCPay depends on. When the sender has a change output, `PayjoinClient.RequestPayjoin` sets `AdditionalFeeOutputIndex` to that output and sets `MaxAdditionalFeeContribution` to the default `originalFeeRate.GetFee(110)` whenever the host leaves `MaxFeeBumpContribution` null, which BTCPay does. So BTCPay offers a contribution by default, 110 vB times the original fee rate, about 1,100 sats at 10 sat/vB. fee-c concluded zero because it saw BTCPay pass no client parameter and missed the library default. The genuine zero default senders are the bitcoinjs client with no integrator cap and the react-native wrapper, which passes a zero fee rate to `buildRecommended`; both are still exposed to the original fee diversion, which needs no contribution.

## Worst case range across deployments in one line

Under the settled bound, at 10 sat/vB on a typical 141 vB sender transaction, exposed senders land around 1,900 sats (original fee of about 1,200 plus roughly one input's fee), BTCPay around 2,300, the react-native and bitcoinjs zero contribution cases around 1,200 by diversion alone, the Bull exchange around 1,200 on a small withdrawal but far more on a consolidation, and bitmask and Trezor are blocked outright; the exchange and bitmask only stay safe if their signer is taproot, which is open question 1.
