# State

Last rewritten: 2026-11-05 (after the wT:p1 cron fix hand-off)

## payjoin-audit (nightly multi model audit of Payjoin implementations)

- master at bc00719 (branch parallel merged: concurrent finding processing, concurrency 24/6, backoff on 429s, raced discovery, flock in run.sh). A resumed full run `bin/run.sh --name 2026-11-05` started 15:10 UTC in wT:p3 (pid 402432); a watcher writes inbox/payjoin-run-done.md when it exits. Reuses the 80 audit and 100 triage answers of the killed run. Cron at 02:17 is safe to overlap now (flock). 18 drafts in reports/drafts/ still await human review. Worker wT:p1 is done; worktree ~/repos/payjoin-audit-parallel can be removed once the run has proven the branch.
- At 15:11, a minute in: 25 model processes, load 26 on 4 cores, 11 GB used, 7 GB available. A watcher leaves inbox/payjoin-memory.md if MemAvailable drops under 1.5 GB. pgrep -f bin/run.sh can match a transient shell; take the pid from `pgrep -af` and confirm it reads `bash bin/run.sh`.
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