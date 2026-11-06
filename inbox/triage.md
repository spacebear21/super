# triage: review comments applied and answered

Branch `triage` in `~/repos/payjoin-audit-triage`, head 0e2b533, three commits on top of the sheet, each lint clean (47 tests, registry check 0 problems). No Claude subagents; code read in the clones, PRs and releases through gh, Cake's PR files fetched through the GitHub API.

| commit | content |
|---|---|
| 305fa24 | Human review comments on the 2026-11-06 sheet, verbatim |
| b291147 | Apply the approved 2026-11-06 decisions |
| 0e2b533 | Answer the human's review comments (`reports/triage/2026-11-06-answers.md`) |

## What was applied (b291147)

- Q5 to Q9 and Q11 as recommended: the escaped twin class retired and F-0316 folded into F-0219; F-0252, F-0263, F-0311 irrelevant with a pointer to F-0217; F-0295, F-0290, F-0292, F-0323, F-0255 folded; F-0281 to medium, F-0308 and F-0306 to low.
- The 23 uncommented reading-list findings carry an "approved for sending (decisions 2026-11-06)" history note; their status stays drafted until the reports go out, then `set-status reported --url`.
- Q10: F-0303 stays low (it already was).
- F-0221: status reported, channel out-of-band, reason records your out-of-band report. The "v0 senders" wording came from my 2026-11-05 triage report, not the draft; both sheets now say every sender is affected.
- F-0256: invalid. Every Bull receive session is created by a user action (receive screen, or a buy order the user places); no payjoin in autobuy or DCA, no background receiver. The reason text says so.
- Commented rows did not move; F-0298 and F-0317's severity changes were held back with them.

## Proposed action per answered comment

- F-0270 (SatoshiPortal): send as is, high; #1971's classify_tx is exactly the fix, unreleased in the JS bindings, interim fix is to compare the callback transaction's inputs with the stored original.
- F-0262, F-0282 (req-pj): send both as is, medium; bitcoin_uri already rejects unknown req- keys and PDK's extras do not claim req-pj; the wallets parse URIs themselves; add a one-line rust-payjoin issue to claim req-pj and a comment on bitcoin_uri #12.
- Unify: hold for your call; recommend one combined private report to the author (Peter Denton, App Store "Unify - Payjoin Wallet", 0 ratings, hand-written Swift, last commit 2025-11-26), then watch.
- BitMask: hold; recommend watch plus one public issue pointing at branch HB/update-2026 (payjoin 1.0.0, payment.rs rewritten, which removes F-0014 and F-0250 when merged).
- F-0244 (pjos=0 dropped): confirmed for the default v2 build on BIP78 URIs (from_parts hardcodes Enabled, CLI never disables); send as is with that wording.
- F-0302 (inserted output): drop, invalid; BIP78 forbids adding receiver outputs when substitution is disabled.
- F-0305 (no error reply): send as is, medium, reworded: parse-stage failures close the session without a reply although the reply key is known; checklist failures do reply.
- F-0315 (Monitor lost after expiry): send as is, medium; #1971 is in the audited commit and construct_history still errors on every non-Closed session.
- F-0225 (unbounded bodies): send as is with F-0200; #1983 checks the size after collecting the body, relay unchanged.
- Cake: send one report now as sprint input, two lists: fixed by PR 3363 (F-0283, F-0294, F-0298, F-0304, F-0279, F-0295, the plugin pin) and still present (F-0179, F-0282, F-0312 since the PR pins payjoin 1.0.0, F-0324, F-0322); hold F-0016 and F-0018, archive the two payjoin-flutter projects once 3363 merges.
- F-0008 (bitcoinjs): re-address to BlueWallet, which vendored the client on 2026-10-20 and is active; one issue with the scheme check, the npm bindings as the long-term path, F-0049 and F-0052 folded in.
- Phobia: send F-0167 with F-0190 as one public issue; active one-person beta wallet with tens of downloads per release, hand-written C# sender.
- F-0317 (Sparrow sighash): downgrade to low and hold with F-0251; a receiver-set sighash is overwritten with the original's before signing, the residual is the user's own SINGLE choice.

Approve the lot or pick; each line maps to a set-status or severity edit I run on your word.
