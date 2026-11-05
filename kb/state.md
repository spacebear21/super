# State

Last rewritten: 2026-11-05 (after the wT:p1 cron fix hand-off)

## payjoin-audit (scheduled multi model audit of Payjoin implementations)

- The 2026-11-05 full run finished at 20:20 UTC and committed on master. 231 new drafts under reports/drafts (6 critical, 46 high, 95 medium, 76 low, 9 info) plus the 18 earlier ones await the human's review; 150 projects were added by triage (registry now 469). The refute stage ran mostly on nemotron-3-ultra (465 calls) because this run still ordered by speed; the capability ordering and kimi-k3.1-pro apply from the next run. Zero refusals in about 700 censored-model calls. kimi-k3 was disabled mid run after repeated failures.
- Registry is clean (0 problems) after the merge fix: duplicate BTCPay projects folded, 11 findings found twice folded into the lower ids (their drafts remain under reports/drafts with the old project id in the name), Bull's exchange entries active.
- Cron runs bin/run.sh at 02:17 UTC nightly; the flock makes overlap safe. Worth checking the next morning: the Refusals section and the model table of reports/runs/<date>/summary.md, and whether kimi-k3.1-pro's stray close token broke any JSON.
- Triage pass in flight (human asked): wT:p1 (now named triage, the session was killed and resumed after it fanned out ten Claude subagents) on branch triage, Claude subagents forbidden, opencode for bulk reading; reports/triage/2026-11-05.md with a reading list of 20, Bull first; note triage.md.
## rust-payjoin

- Native FFI builds PR 2023: the human force pushed the six commit version (rebased on master) on 2026-11-05 and asked Bull Bitcoin's Dart maintainer whether the Dart package should keep a build from source mode beside the default prebuilt libraries (reasoning in projects/rust-payjoin.md). Waiting on that answer; then the human picks, and the phase2 worker (wS:pP) prepares the prebuilt only change if that is the choice. Nothing else pending.
- witness_utxo fee fix (wS:pR, idle): two finished commits on a local branch (reject contradicting UTXO data; require the previous transaction on receiver inputs unless the sender's taproot signing covers amounts). The human rejected this shape on 2026-11-05 (breaks honest Bull and Cake receivers; severity does not justify it), moved the private PR to draft; the worker was told to stand down.
- Rethink complete: synthesis.md, fundamentals.md, eight discovery reports and experiment-taproot-amount.md under ~/review-drafts/untrusted-inputs/ (backups in untrusted-inputs-backup). Conclusions filed in projects/rust-payjoin.md. Waiting on the human's decision on the path (the synthesis's universal fixes plus accepting the bounded residual) and on asking Bull whether their exchange sending wallet signs taproot. The seven rethink agents (fundamentals, synthesis, discovery-a to e in wS tabs tX and tY; discovery-c still running on kimi-k3) and the worktree ~/repos/rust-payjoin-untrusted-inputs stay until the human is done with the reports; then release them.
- Decision context from the other finished wS workers (fallback_tx review, security audit) is filed in projects/rust-payjoin.md.
- rust-payjoin-red-team: the wT:p1 worker that built payjoin-audit is done (see payjoin-audit above).

## ldk-node

- Three opencode workers done in tab wS:t8: ChannelMonitor output tracking, funding candidate conflicts, Node wallet transaction classification. Not yet reviewed by super.

## harness

- Hub is the bare repo ~/repos/harness.git, main at cece2c4. Workspace wV and its worker are gone; the super agent now sits in workspace wW (label super) as wW:p1. Harness work goes in workspace wX (label harness).
- super-watch stale note fix: done, merged into hub main and installed live at ~16:50 (the restart loop picked the new copy up). Worker wX:p1 (superwatch-fix) is idle and can be released; worktree ~/repos/harness-super-watch-stale can go, branch stays for the human to fetch.
- wX:p2 (claude, name scheduler) is on branch scheduler in ~/repos/harness-scheduler: committed schedule file, cron in the image, schedule-refresh (crontab block plus task repo clones) at boot and on push to main, runbook section; the payjoin audit at 17 02 UTC is the first entry. When its note lands: review, merge into hub main, then have payjoin-audit drop bin/install-cron.sh and image/, and decide with the human about the image deploy (cron in the image needs a rebuild).
- Waiting on the human's laptop: deploy the image (infra/scripts/workhorse-deploy.sh), which replaces the hand-run super-watch loop (pid 454) with the entrypoint's own; create the private repo spacebear21/super (empty, Actions disabled) and a fine-grained PAT with Contents read and write on it, set with workhorse-deploy.sh --set-secrets, so super-sync can push this memory; /login in the super session before it expires; /rc if Remote Control is wanted. Until the deploy, the scheduler branch's cron support is in the hub only.
