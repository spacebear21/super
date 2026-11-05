# State

Last rewritten: 2026-11-05 (after the wT:p1 cron fix hand-off)

## payjoin-audit (nightly multi model audit of Payjoin implementations)

- master at dd270ad, cron live at 02:17 UTC calling bin/run.sh. The full hand run of 2026-11-05 (02:55 to 14:57 UTC) was killed by the human: 320 raw findings were being deduped and refuted one at a time, 24 h to go. Its logged answers (80 audit, 80 triage, 6 discover, 52 dedupe, 69 refute, 15 tiebreak) sit in ~/.cache/payjoin-audit/calls/2026-11-05 and are reused by a run started with --name 2026-11-05. registry/projects.json holds uncommitted triage results from that run; run.sh commits them before the next run. 18 drafts in reports/drafts/ still await human review.
- wT:p1 (claude) is parallelizing the pipeline on branch parallel in ~/repos/payjoin-audit-parallel (brief in log.md, 2026-11-05 ~15:05). When its note lands: review, merge into master, then rerun with `bin/run.sh --name 2026-11-05` in wT:p3 before 00:00 UTC if possible (else keep --name). The cron at 02:17 must not overlap a running hand run until the flock lands; comment the crontab line out if a hand run will still be going then.
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