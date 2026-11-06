# triage: two interop findings filed against rust-payjoin

Branch `triage` in `~/repos/payjoin-audit-triage`, fast-forwarded to master 14746e3 first, head bcb1bd51c7dd2585752f848343bc57aa1055b417. Lint clean (47 tests, registry check 0 problems: 327 findings). Nothing in `~/repos/payjoin-audit-interop` touched.

| id | finding | class | severity | reproduction |
|---|---|---|---|---|
| F-0327 | Receiver contribution panics when the Core wallet holds a taproot coin: `WeightError(NotSupported)` at payjoin-cli/src/app/wallet.rs:234 | bip78-receiver-skip-unsupported-coins | low | cells A08, A09 (every cell with a taproot receiver inherits it) |
| F-0328 | Sender precheck panics when the Core wallet holds a nested SegWit (or taproot) coin: `WeightError(NoRedeemScript)` at the same line, before anything is posted | bip78-any-input-weight-estimation (instance low; the class's high is for fee mispricing) | low | cell B09 (and A09 on the sender side) |

Drafts: `reports/drafts/2026-11-06-rust-payjoin-F-0327.md` and `-F-0328.md`, usual form, source `interop-matrix` with the scenario ids in `found_by.scenarios`.

Quoted line at tag payjoin-cli-1.0.0 (e4518534): `InputPair::new(txin, psbtin, None).expect("Input pair should be valid")`, reached from `list_unspent`, which maps every `listunspent` row eagerly; the receiver reaches it from `contribute_inputs` (v2/mod.rs:1098) and `try_contributing_inputs` (v1.rs:423), the sender from `has_spendable_utxos` (wallet.rs:205) at the start of every send (app/mod.rs:50). Nested SegWit fails because the helper sets `redeem_script: None` although Core reports it; taproot fails because the library cannot size an unsigned P2TR input (psbt/mod.rs:268-276).

Worth knowing: both are already fixed on master, unreleased, by 03f1ddbc "Handle p2tr key-spend weights" and 3989c828 "Filter invalid coins from payjoin-cli when validating the input weights" (both 2026-10-12, no CLI tag contains them). The drafts say so, so the report is a release ask rather than a bug report. The matrix cells A08, A09 and B09 should pass once the participant pins a commit past 3989c828.
