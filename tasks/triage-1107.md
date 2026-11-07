# Decision sheet for the 37 drafts of 2026-11-07

## What it is
The first cron audit run (2026-11-07) wrote 37 drafts. An Opus worker read them against the clones and wrote the sheet at `reports/triage/2026-11-07-decisions.md` on payjoin-audit master, in the shape of the 2026-11-06 sheet. Of the 37: 16 real (14 to send or fold into a report, 2 held), 21 dropped, one rescue from the refuted pile, one PR hint held and referred to super because it touches the private fee-check area. None of the seven drafted highs survived as a high.

## What is asked
Eleven questions, each with a recommendation in the sheet. Answer "approve all" or name exceptions by number.

1. Q1 Bull mobile: approve four verdicts (one repeat of F-0210, one hold next to F-0254, one fold into F-0219, one drop). Recommended: approve.
2. Q2 Bull mobile, rescue (medium): with payjoin on by default, a BIP21 with a BIP78-only pj (every BTCPay store with payjoin enabled) makes the send abort instead of paying plainly. Verified in the code. Recommended: rescue and send.
3. Q3 rust-payjoin: twelve verdicts; two fold into the mailroom report (not yet sent, so they fold in), one payjoin-cli robustness issue, F-0336 rides with F-0315, one small-fixes issue, four drops, one invalid; F-0336 high to low, F-0345 medium to low. Recommended: approve.
4. Q4 bdk-cli: one low robustness issue, three drops. Recommended: approve.
5. Q5 BlueWallet: change last night's not-yet-sent report to lead with F-0330 (its vendored client rejects every proposal that keeps sender UTXO data, which bips#1396 allows and rust-payjoin receivers do, so BlueWallet cannot complete a payjoin with any current receiver) and demote F-0008 to low (iOS ATS and the Android network config already block cleartext). Recommended: yes.
6. Q6 JoinInBox: re-scope to JoinMarket's own disclosure. It installs JoinMarket v0.9.12 and offers payjoin send; JoinMarket (archived) disclosed on 2026-11-04 a sighash theft bug in that send path that will never be fixed. A public issue asking JoinInBox to remove the option or show the warning. Recommended: yes.
7. Q7 LtbLightning payjoin-ffi and payjoin-flutter: drop the five drafts now (each restates a held library finding). Recommended: drop.
8. Q8 one-liners: bark as one low public question, trezor held with its siblings, the bitcoinjs pair and BlueWallet's toggle draft dropped. Recommended: approve.
9. Q9 fungi #183: post none of the three HPKE comment drafts; they are wrong, fungi runs its own protocol over BIP77 mailboxes. Optional: one short question about uniform payloads (text in the sheet). Recommended: post none; the question is your call.
10. Q10 FullyNoded #282: post one rewritten comment for F-0364 (minfeerate parsed with Double accepts nan and inf and the Int64 conversion traps, so a URI can crash the app), drop F-0363. Recommended: yes.
11. Q11 retire three noisy classes (pjos for v2 senders, poll loop without backoff, the third sign-only twin). Recommended: retire all three.

## Where it stands
The registry is unchanged until you answer; section F of the sheet holds the commands, tested on a copy. The pipeline problems the sheet found (same model in two refuter seats, models writing files into the clones, dedupe missing same-location repeats, summary paths) are being fixed by a Codex worker on branch run-hygiene, to merge before tonight's run.
