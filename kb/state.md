# State

Last rewritten: 2026-11-05 (after the wT:p1 cron fix hand-off)

## payjoin-audit (nightly multi model audit of Payjoin implementations)

- master at 526e139 (parallel finding processing, concurrency 24/6, backoff on 429s, raced discovery, flock, no kimi race, censored-first with refusal fallback and refusal stats, pipelined finding processing). Run `bin/run.sh --name 2026-11-05` restarted 16:11 UTC in wT:p3 (pid 477357); a watcher writes inbox/payjoin-run-done.md on exit and inbox/payjoin-memory.md if MemAvailable drops under 1.5 GB. Reuses all logged discovery, triage and audit answers. When it lands: read reports/runs/2026-11-05/summary.md, especially the Refusals section and the model table (refused column), and report both to the human; false positives of the refusal regex are the thing to look for. Cron at 02:17 is safe (flock). 18 drafts still await human review. Worker wT:p1 is done. Registry fixes merged (master bc1935b). AFTER the run commits: `cd ~/repos/payjoin-audit && python3 -I bin/audit check --fix`, review the merged lines, commit registry/.
- At 16:29 the run was in the refute stage with 21 model processes, 46 refute seats in 18 min, 0 refusals so far, 10 GB memory available.
- Right after `herdr pane run`, pgrep -f bin/run.sh first matches a wrapper that exits within seconds; wait a few seconds, then take the pid whose `ps -o args` reads `bash bin/run.sh ...` and which still exists 10 s later.
- Pane wT:p3 (tab '1', shows as opencode, codex or claude) is the shell running bin/audit; Herdr flips its status as the pipeline spawns codex calls, so 'opencode in 1 is now idle' wake-ups are noise while the run log keeps advancing.
- Headless refute workers inherit the global hand-off rule and write notes to ~/super/inbox/ named after the audited repo's branch (first seen: master.md, 00:32). Their result is already in ~/.cache/payjoin-audit/calls/<run>/*.out.json. Treat such notes as noise: read, confirm the match, delete.

## rust-payjoin

- PR #2016 native builds: phase2 (wS:pP) finished native-build-6 at bb0a47f9 with report ~/repos/native-build-6-REPORT.md; The open PR is #2023 (head spacebear21:native-build at 9c01ab79), not #2016. Recommendation to the human: force push native-build-6 (bb0a47f9) to spacebear21:native-build and paste the PR description from ~/repos/native-build-6-PR-2023.md. The remote branch holds nothing beyond the rebased nine commits. Waiting on the human. Details in projects/rust-payjoin.md.
- Other wS workers not yet reviewed by super: fallback_tx exposure in monitor typestate (wS:pH, done), BIP-78 witness_utxo fee gap (wS:pR, idle), security audit of BIP78/BIP77 (wS:p11, opencode, said "Nothing needed").
- rust-payjoin-red-team: the wT:p1 worker that built payjoin-audit is done (see payjoin-audit above).

## ldk-node

- Three opencode workers done in tab wS:t8: ChannelMonitor output tracking, funding candidate conflicts, Node wallet transaction classification. Not yet reviewed by super.

## harness

- Hub is the bare repo ~/repos/harness.git, main at cece2c4. Workspace wV and its worker are gone; the super agent now sits in workspace wW (label super) as wW:p1. Harness work goes in workspace wX (label harness).
- super-watch stale note fix: done, merged into hub main (49d3408, db33d53) and installed live at ~16:50 (the restart loop picked the new copy up). Worker wX:p1 (superwatch-fix) is idle and can be released; worktree ~/repos/harness-super-watch-stale can go, branch stays for the human to fetch.
- wX:p2 (claude, name scheduler) is on branch scheduler in ~/repos/harness-scheduler: committed schedule file, cron in the image, schedule-refresh (crontab block plus task repo clones) at boot and on push to main, runbook section; the payjoin audit at 17 02 UTC is the first entry. When its note lands: review, merge into hub main, then have payjoin-audit drop bin/install-cron.sh and image/, and decide with the human about the image deploy (cron in the image needs a rebuild).
- Earlier: super-record (a384070 in hub main) made ~/super the repo and the notes-and-dialogs watcher live. Laptop steps for the human in projects/harness.md: image deploy, the private spacebear21/super repo and PAT, /login, /rc.
