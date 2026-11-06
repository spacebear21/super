# State

Last rewritten: 2026-11-06 23:50 UTC by the dreaming pass, each line against the source named in it.

## payjoin-audit (scheduled multi model audit of Payjoin implementations)

- Nightly run at 02:17 UTC, now through `schedule-run` from the crontab's rendered block [checked: crontab]. Master carries everything merged on 2026-11-06: guard rails (preflight, minority seats never silent, auth disables a model, failure-rate watchdog, one notify helper writing an inbox note per run), discovery alongside the scan, refute seats on different families, glm-5.3 leading the strong order, kimi-k3 and muse-spark-open dropped, the PR review stage capped at five a night [checked: master log]. None of it has run from cron yet.
- Morning check after the 2026-11-07 run: the run's inbox note, the Refusals section and model table of reports/runs/<date>/summary.md, the "shares its family with another seat" line and refute wall clock in the log, the first PR review drafts under reports/drafts/pr/.
- Run 2026-11-06 (cron start died on the missing key, restarted by hand 03:06, done 05:26): 80 tasks, 75 drafts, 20 candidates added by discovery, 19 excluded and one (morphit) active [checked: reports/runs/2026-11-06/summary.md]. Its drafts were triaged in the second sheet.
- Second sheet closed, merged to master with the Cake PR 3363 review; the two payjoin-cli 1.0.0 crashes the matrix found are filed as F-0327 and F-0328 (low, fixed upstream, unreleased) [checked: master log]. Triage worker released. What waits on the human (holds, approved batch, the Cake comment, the receiver fee class) is on the board. The reading list (Q37) is revisited later by a fresh Opus worker once the important projects are covered.
- Unsettled from the sheet: whether the Dart bindings panic or throw on a v1 URI; F-0257 after BitMask's rewrite; bullbitcoin-mobile code_paths too broad; open_prs on forks skipped.
- Interop matrix: building, wT:p8 interop-build (Claude Opus) on branch interop in ~/repos/payjoin-audit-interop, deliverables 5 to 7 from ~/review-drafts/interop/handover-brief.md; six commits on the branch, more in progress uncommitted [checked: worktree, agent list]. Merges to master when its hand-off reports suite and lint green and the nightly script in place.
- No GitHub repository exists for payjoin-audit [checked: gh]; the schedule's repo field is a placeholder until the human creates one or changes the field. bin/install-cron.sh and image/ can be dropped from the repo once the image deploy lands.

## rust-payjoin

- witness_utxo fee issue: path decided, team brief written, waiting on the human to open the team discussion (see kb/topics/witness-utxo-fee-issue.md). Panes that stay until the human is done with the reports: synthesis wS:p2Y (done), fundamentals wS:p2R, discovery-a to e wS:p2S to p2X (all idle, none still running), the fee fix worker wS:pR (idle, stood down), worktree ~/repos/rust-payjoin-untrusted-inputs [checked: agent list].
- Native FFI builds PR 2023: merged upstream 2026-11-06 20:29 UTC [checked: gh]. Nothing waits on it here; the PR 1996 vs 1815 pane wS:pB is idle and can be released.
- Receiver settlement PR 1971: merged 2026-11-05; follow-ups in open issue 2034 [checked: gh]. Optional for the human: post the comment adding the one follow-up the issue misses (the SessionStatus rustdoc caveat), draft at ~/review-drafts/payjoin-1971/draft-issue-2034-completed.md.
- The OpenCode security audit pane wS:p11 is idle; its conclusions are on the project page; it can be released.

## ldk-node

- Three research reports under ~/review-drafts/ldk-research/, summarized on the project page; not yet reviewed by super.

## harness

- Memory (w11:p2, name memory, Claude Opus, status done): branch memory in ~/repos/harness-memory holds transcripts-index, super-dream and the 05:30 UTC schedule entry, committed 23:35 to 23:37; a runbook edit is uncommitted and the hand-off note has not arrived [checked: worktree, inbox]. The transcripts collection is indexed and this pass ran from it. Next: merge memory into hub main after the hand-off; super keeps reshaping kb into topic pages.
- Dashboard board: in hub main and live through the post-receive hook; worker released [checked: hub main log].
- Scheduler and scheduled panes: in hub main. Stopgap on the box until the image deploy: schedule-run and schedule-refresh hand-copied into /usr/local/bin, the hook given its schedule-refresh line by hand, the crontab holding only the rendered block, the hand-installed audit line gone [checked: hub docs/progress.md, crontab]. Side note from the scheduler worker: a tracked .pyc under infra/workhorse/dashboard was ignored since.
- Deploy, waiting on the human's laptop: fetch hub main so infra/workhorse is current, run infra/scripts/workhorse-deploy.sh outside the 02:17 UTC window; the deploy replaces this container and the super session comes back through Herdr's restore. The ~/super repository exists on GitHub and pushes from this box succeed [checked: origin/main]; whether the image's super-sync secret is set is [unconfirmed]. /login before it expires.
- Workspaces by label: rust-payjoin wS, payjoin-audit wT, super wW, harness w11 (ids have changed before; the label is what the rule names) [checked: herdr workspace list].
