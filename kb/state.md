# State

Last rewritten: 2026-11-05 (after the wT:p1 cron fix hand-off)

## payjoin-audit (nightly multi model audit of Payjoin implementations)

- Pipeline now parallel end to end with censored-first models and refusal fallback. Run `bin/run.sh --name 2026-11-05` restarted 16:11 UTC in wT:p3 (pid 477357); a watcher writes inbox/payjoin-run-done.md on exit and inbox/payjoin-memory.md if MemAvailable drops under 1.5 GB. Reuses all logged discovery, triage and audit answers. When it lands: read reports/runs/2026-11-05/summary.md, especially the Refusals section and the model table (refused column), and report both to the human; false positives of the refusal regex are the thing to look for. Cron at 02:17 is safe (flock). 18 drafts still await human review. Worker wT:p1 is done. Registry fixes merged. AFTER the run commits: `cd ~/repos/payjoin-audit && python3 -I bin/audit check --fix`, review the merged lines, commit registry/.
- Follow-up after the run lands: in the audit registry, satoshiportal-payjoin (the exchange side sender and receiver) and bull-bitcoin-exchange are only "watch"; the 2026-11-04 blog post shows them in production, so promote them to active with the post as evidence. At 16:29 the run was in the refute stage with 21 model processes, 46 refute seats in 18 min, 0 refusals so far, 10 GB memory available.
- Right after `herdr pane run`, pgrep -f bin/run.sh first matches a wrapper that exits within seconds; wait a few seconds, then take the pid whose `ps -o args` reads `bash bin/run.sh ...` and which still exists 10 s later.
- Pane wT:p3 (tab '1', shows as opencode, codex or claude) is the shell running bin/audit; Herdr flips its status as the pipeline spawns codex calls, so 'opencode in 1 is now idle' wake-ups are noise while the run log keeps advancing.
- Headless refute workers inherit the global hand-off rule and write notes to ~/super/inbox/ named after the audited repo's branch (first seen: master.md, 00:32). Their result is already in ~/.cache/payjoin-audit/calls/<run>/*.out.json. Treat such notes as noise: read, confirm the match, delete.

## rust-payjoin

- Native FFI builds PR 2023: the human force pushed the six commit version (rebased on master) on 2026-11-05 and asked Bull Bitcoin's Dart maintainer whether the Dart package should keep a build from source mode beside the default prebuilt libraries (reasoning in projects/rust-payjoin.md). Waiting on that answer; then the human picks, and the phase2 worker (wS:pP) prepares the prebuilt only change if that is the choice. Nothing else pending.
- witness_utxo fee fix (wS:pR, idle): two finished commits on a local branch (reject contradicting UTXO data; require the previous transaction on receiver inputs unless the sender's taproot signing covers amounts). The human rejected this shape on 2026-11-05 (breaks honest Bull and Cake receivers; severity does not justify it), moved the private PR to draft; the worker was told to stand down.
- Rethink done: synthesis at ~/review-drafts/untrusted-inputs/synthesis.md (plus fundamentals.md and eight discovery reports; backups in untrusted-inputs-backup). Headline: signature verification of receiver inputs is defeated by key grinding, demonstrated on the consensus verifier; recommendation is sound hardening that breaks no receiver plus accepting the bounded loss, with one experiment left (how Cake and Bull senders sign an inflated taproot input). Waiting on the human's read. discovery-c (CoinJoin, kimi-k3) still running; file its note when it lands, then release the six rethink agents (fundamentals, synthesis, discovery-a to e in wS tabs tX and tY) and the worktree ~/repos/rust-payjoin-untrusted-inputs once the human is done with the reports.
- Decision context from the other finished wS workers (fallback_tx review, security audit) is filed in projects/rust-payjoin.md.
- rust-payjoin-red-team: the wT:p1 worker that built payjoin-audit is done (see payjoin-audit above).

## ldk-node

- Three opencode workers done in tab wS:t8: ChannelMonitor output tracking, funding candidate conflicts, Node wallet transaction classification. Not yet reviewed by super.

## harness

- Hub is the bare repo ~/repos/harness.git, main at cece2c4. Workspace wV and its worker are gone; the super agent now sits in workspace wW (label super) as wW:p1. Harness work goes in workspace wX (label harness).
- super-watch stale note fix: done, merged into hub main and installed live at ~16:50 (the restart loop picked the new copy up). Worker wX:p1 (superwatch-fix) is idle and can be released; worktree ~/repos/harness-super-watch-stale can go, branch stays for the human to fetch.
- wX:p2 (claude, name scheduler) is on branch scheduler in ~/repos/harness-scheduler: committed schedule file, cron in the image, schedule-refresh (crontab block plus task repo clones) at boot and on push to main, runbook section; the payjoin audit at 17 02 UTC is the first entry. When its note lands: review, merge into hub main, then have payjoin-audit drop bin/install-cron.sh and image/, and decide with the human about the image deploy (cron in the image needs a rebuild).
- Waiting on the human's laptop: deploy the image (infra/scripts/workhorse-deploy.sh), which replaces the hand-run super-watch loop (pid 454) with the entrypoint's own; create the private repo spacebear21/super (empty, Actions disabled) and a fine-grained PAT with Contents read and write on it, set with workhorse-deploy.sh --set-secrets, so super-sync can push this memory; /login in the super session before it expires; /rc if Remote Control is wanted. Until the deploy, the scheduler branch's cron support is in the hub only.
