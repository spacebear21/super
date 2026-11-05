# triage: reading list for the 2026-11-05 drafts

Branch `triage`, worktree `~/repos/payjoin-audit-triage`, off master bbaae5b, head cfec4d1, one commit: `reports/triage/2026-11-05.md`. No finding status, draft or registry file changed. Lint clean, registry check 0 problems.

## Top five, one line each

1. **F-0219 rust-payjoin (high).** payjoin-cli signs every wallet-owned input and the library treats an empty final witness as finalized, so a receiver who knows another sender UTXO gets it signed and can redirect it; Bull Bitcoin should check its own `bull_payjoin` signer for the same pattern.
2. **F-0221 rust-payjoin (medium, merge F-0205).** Sender fee checks trust receiver-supplied witness_utxo values; a receiver can skim up to the original fee from segwit v0 senders; `validate_utxo` already exists in the crate.
3. **F-0244 rust-payjoin (medium, merge F-0241).** v1 `SenderBuilder::from_parts` drops pjos=0 and payjoin-cli uses it, so a malicious directory on the cleartext v1 path can redirect payment; payjoin-ffi refuses v1 so FFI consumers are safe.
4. **F-0016 payjoin-ffi and flutter (medium, with F-0017 to F-0019).** The plugin chain pins payjoin 0.23.0; Bull Bitcoin mobile uses the Dart `payjoin` package 0.25.1, newer but still before the 1.1.0 and 1.2.0 fixes (URL spoofing, receiver panic, sender input-order check); confirm what 0.25.1 wraps.
5. **F-0179 cake-wallet (medium) with F-0093 and F-0091 unify-wallet (high).** Three consumers sign whatever proposal input matches a wallet UTXO; Cake silently loses the payment, Unify's output guard only checks the last original output so change can be stolen (I verified that line of Swift myself).

## What the file holds

Reading list of 19 in order (sections 1), 9 false positives and 20 inflated groups with reasons (2), two dissent cases to rescue or reconcile (3), the 11 folded duplicates and the near duplicates across BTCPay, the Flutter chain and camlcoin (4), 153 parked findings by project and severity with the model verdict (5).

## Caveats

- bull-bitcoin-mobile, satoshiportal-payjoin and bull-bitcoin-exchange were never scanned (no last_scanned, zero findings); the Bull Bitcoin priority is served through their dependencies only. Scanning them is the obvious next run.
- Method after the budget stop: two reader reports (rust-payjoin, 26 camlcoin drafts) reused from scratch; the other 175 drafts each verified against the clone by tff/kimi-k3.1-pro (high and critical) or tff/glm-5.3; these models never answered "false-positive" once, so section 2 rests on the readers and my own checks. Their verdicts in section 5 are advisory.
- camlcoin's 55 drafts come from a scaffold whose shipped receiver never contributes a UTXO; I recommend four issues there, not 55.
- F-0014 bitmask (critical as drafted) is unreachable today because F-0250 makes every BitMask payjoin fail at signing; one issue for both.
- Dissent case worth a human's five minutes: BTCPay BIP78.Sender never checks PSBT_IN_SIGHASH; whether that matters depends on NBitcoin's signing paths honouring the field.
