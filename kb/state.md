# State

Last rewritten: 2026-11-06 23:50 UTC by the dreaming pass, each line against the source named in it.

## payjoin-audit (scheduled multi model audit of Payjoin implementations)

- Nightly run at 02:17 UTC, now through `schedule-run` from the crontab's rendered block [checked: crontab]. Master carries everything merged on 2026-11-06: guard rails (preflight, minority seats never silent, auth disables a model, failure-rate watchdog, one notify helper writing an inbox note per run), discovery alongside the scan, refute seats on different families, glm-5.3 leading the strong order, kimi-k3 and muse-spark-open dropped, the PR review stage capped at five a night [checked: master log]. None of it has run from cron yet.
- Morning check after the 2026-11-07 run: the run's inbox note, the Refusals section and model table of reports/runs/<date>/summary.md, the "shares its family with another seat" line and refute wall clock in the log, the first PR review drafts under reports/drafts/pr/.
- Run 2026-11-06 (cron start died on the missing key, restarted by hand 03:06, done 05:26): 80 tasks, 75 drafts, 20 candidates added by discovery, 19 excluded and one (morphit) active [checked: reports/runs/2026-11-06/summary.md]. Its drafts were triaged in the second sheet.
- Second sheet closed, merged to master with the Cake PR 3363 review; the two payjoin-cli 1.0.0 crashes the matrix found are filed as F-0327 and F-0328 (low, fixed upstream, unreleased) [checked: master log]. Triage worker released. What waits on the human (approved batch, the Cake comment, the receiver fee class) is on the board.
- Holds (from the second triage sheet): SatoshiPortal F-0270/F-0272/F-0276/F-0278 until rust-payjoin 1.3 (report with upgrade guidance), Unify's three, BitMask's three (project moved to watch), F-0317 with F-0251, payjoin-flutter F-0016/F-0018 until Cake's PR 3363 merges. [checked: main's state before the dream merge, from the triage worker's hand-off] The reading list (Q37) is revisited later by a fresh Opus worker once the important projects are covered.
- Unsettled from the sheet: whether the Dart bindings panic or throw on a v1 URI; F-0257 after BitMask's rewrite; bullbitcoin-mobile code_paths too broad; open_prs on forks skipped.
- Interop matrix: building, wT:p8 interop-build (Claude Opus) on branch interop in ~/repos/payjoin-audit-interop, deliverables 5 to 7 from ~/review-drafts/interop/handover-brief.md; eight commits on the branch, more in progress uncommitted [checked: worktree, agent list]. Merges to master when its hand-off reports suite and lint green and the nightly script in place.
- No GitHub repository exists for payjoin-audit [checked: gh]; the schedule's repo field is a placeholder until the human creates one or changes the field. bin/install-cron.sh and image/ can be dropped from the repo once the image deploy lands.

## rust-payjoin

- witness_utxo fee issue: path decided, team brief written, waiting on the human to open the team discussion (see kb/topics/witness-utxo-fee-issue.md). Panes that stay until the human is done with the reports: synthesis wS:p2Y (done), fundamentals wS:p2R, discovery-a to e wS:p2S to p2X (all idle, none still running), the fee fix worker wS:pR (idle, stood down), worktree ~/repos/rust-payjoin-untrusted-inputs [checked: agent list].
- Native FFI builds PR 2023: merged upstream 2026-11-06 20:29 UTC [checked: gh]. Nothing waits on it here; the PR 1996 vs 1815 pane wS:pB is idle and can be released.
- Receiver settlement PR 1971: merged 2026-11-05; follow-ups in open issue 2034 [checked: gh]. Optional for the human: post the comment adding the one follow-up the issue misses (the SessionStatus rustdoc caveat), draft at ~/review-drafts/payjoin-1971/draft-issue-2034-completed.md.
- The OpenCode security audit pane wS:p11 is idle; its conclusions are on the project page; it can be released.

## ldk-node

- Three research reports under ~/review-drafts/ldk-research/, summarized on the project page; not yet reviewed by super.

## harness

- Dashboard pages: on the hub's main 4a05365, home is the conversation under a nav row with Tasks and Agents links, each list on its own page. Worker released.
- Memory: on the hub's main 32cb714 (transcripts-index, super-dream, schedule entries for the dream at 05:30 and the interop matrix at 04:45 UTC, runbook); scripts symlinked into ~/.local/bin until the deploy. The trial dream branch dream/2026-11-06 is under review by an Opus subagent; merge or reject follows. Open with the human: the nightly turn cap. The super agent reshapes kb into topic pages as it goes; the dream pass continues it. Worker released.
- Dashboard board: in hub main and live through the post-receive hook; worker released [checked: hub main log].
- Scheduler and scheduled panes: in hub main. Stopgap on the box until the image deploy: schedule-run and schedule-refresh hand-copied into /usr/local/bin, the hook given its schedule-refresh line by hand, the crontab holding only the rendered block, the hand-installed audit line gone [checked: hub docs/progress.md, crontab]. Side note from the scheduler worker: a tracked .pyc under infra/workhorse/dashboard was ignored since.
- Deploy, waiting on the human's laptop: fetch hub main so infra/workhorse is current, run infra/scripts/workhorse-deploy.sh outside the 02:17 UTC window; the deploy replaces this container and the super session comes back through Herdr's restore. The ~/super repository exists on GitHub and pushes from this box succeed [checked: origin/main]; whether the image's super-sync secret is set is [unconfirmed]. /login before it expires.
- Workspaces by label: rust-payjoin wS, payjoin-audit wT, super wW, harness w11 (ids have changed before; the label is what the rule names) [checked: herdr workspace list].
