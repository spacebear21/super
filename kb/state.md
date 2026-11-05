# State

Last rewritten: 2026-11-05 (after the wT:p1 cron fix hand-off)

## payjoin-audit (nightly multi model audit of Payjoin implementations)

- First limited run done by hand (02:09). master at dd270ad, cron live at 02:17 UTC calling bin/run.sh (renamed from nightly). The 2026-11-05 cron run failed on PATH and was reverted; fixed, next run 2026-11-06 02:17 UTC, worth checking cron.log after it. 18 drafts in reports/drafts/ await human review before anything is sent; see projects/payjoin-audit.md for the ranked ones. The wT:p1 worker is finished; it dropped the OpenCode Zen free models itself (e27c3cc), the human has not confirmed. Scheduled runs commit their own results.
- Pane wT:p3 (tab '1', shows as opencode, codex or claude) is the shell running bin/audit; Herdr flips its status as the pipeline spawns codex calls, so 'opencode in 1 is now idle' wake-ups are noise while the run log keeps advancing.
- Headless refute workers inherit the global hand-off rule and write notes to ~/super/inbox/ named after the audited repo's branch (first seen: master.md, 00:32). Their result is already in ~/.cache/payjoin-audit/calls/<run>/*.out.json. Treat such notes as noise: read, confirm the match, delete.

## rust-payjoin

- PR #2016 native builds: phase2 (wS:pP) finished native-build-6 at bb0a47f9 with report ~/repos/native-build-6-REPORT.md; The open PR is #2023 (head spacebear21:native-build at 9c01ab79), not #2016. Recommendation to the human: force push native-build-6 (bb0a47f9) to spacebear21:native-build and paste the PR description from ~/repos/native-build-6-PR-2023.md. The remote branch holds nothing beyond the rebased nine commits. Waiting on the human. Details in projects/rust-payjoin.md.
- Other wS workers not yet reviewed by super: fallback_tx exposure in monitor typestate (wS:pH, done), BIP-78 witness_utxo fee gap (wS:pR, idle), security audit of BIP78/BIP77 (wS:p11, opencode, said "Nothing needed").
- rust-payjoin-red-team: the wT:p1 worker that built payjoin-audit is done (see payjoin-audit above).

## ldk-node

- Three opencode workers done in tab wS:t8: ChannelMonitor output tracking, funding candidate conflicts, Node wallet transaction classification. Not yet reviewed by super.

## harness

- Super agent first run verified and recorded by the worker in wV:p1 (branch super-record, worktree ~/repos/harness-super, hub main at 44f287d). Worker is done; its scratch fixtures are gone. Follow-up done (a384070 in hub main): ~/super is the repo, notes-and-dialogs watcher live. Laptop steps for the human in projects/harness.md: image deploy, the private spacebear21/super repo and PAT, /login, /rc. No remote copy of memory until the repo and PAT exist.