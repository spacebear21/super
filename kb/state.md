# State

Last rewritten: 2026-11-07 05:40 UTC by the dreaming pass, each line against the source named in it.

## payjoin-audit (scheduled multi model audit of Payjoin implementations)

- Nightly run: tonight's 02:17 UTC start is the first cron run to get past its first minutes; at 05:35 it was still in the refute stage in pane wY:p1 [checked: cron log, process list, run log]. Its notes so far: one burst of eleven OpenCode database lock failures at 02:35 (local, not the models), and one fallback to Codex at 04:16 on a refute seat whose glm fallback had failed [checked: inbox note payjoin-audit-2026-11-07.md]. The seat fix's "shares its family with another seat, no other family left" line fires on glm-5.3-uncensored refute seats [checked: run log 05:34]. Morning check when the run's note lands: the Refusals section and model table of reports/runs/2026-11-07/summary.md, how many calls fell to Claude or Codex, refute wall clock, the first PR review drafts under reports/drafts/pr/ (none existed at 05:31).
- OpenCode isolation (every call in its own data directory, lock errors classified as local): built and tested on branch oc-isolate, not on master; merges when the run ends [checked: branch list, merge-base against master].
- Interop matrix: merged to master on 2026-11-07 and run from cron at 04:45 the same night, 44 cells, 64 pass, 37 fail, 53 unsupported, 24 minutes alongside the audit [checked: reports/interop/2026-11-07.json]. For the next interop task: one result file per UTC date (the hand run and the cron run overwrote each other) and the A18 fee verdict bound; see the topic page. Open with the human: the bitcoinjs UTXO fields finding (board).
- Run 2026-11-06 (cron start died on the missing key, restarted by hand 03:06, done 05:26): 80 tasks, 75 drafts, 20 candidates added by discovery, 19 excluded and one (morphit) active [checked: reports/runs/2026-11-06/summary.md]. Its drafts were triaged in the second sheet.
- Second sheet closed, merged to master with the Cake PR 3363 review; the two payjoin-cli 1.0.0 crashes the matrix found are filed as F-0327 and F-0328 (low, fixed upstream, unreleased) [checked: master log]. Triage worker released. What waits on the human (approved batch, the Cake comment, the receiver fee class) is on the board.
- Holds (from the second triage sheet): SatoshiPortal F-0270/F-0272/F-0276/F-0278 until rust-payjoin 1.3 (report with upgrade guidance), Unify's three, BitMask's three (project moved to watch), F-0317 with F-0251, payjoin-flutter F-0016/F-0018 until Cake's PR 3363 merges [checked: triage worker's hand-off, kept from the previous state]. The reading list (Q37) is revisited later by a fresh Opus worker once the important projects are covered.
- Unsettled from the sheet: whether the Dart bindings panic or throw on a v1 URI; F-0257 after BitMask's rewrite; bullbitcoin-mobile code_paths too broad; open_prs on forks skipped.
- Backup: the audit repo has no GitHub remote; the human makes a repo "once it's in a more stable state, let's see how tonight's runs go" [checked: transcript 2026-11-07 01:53].

## rust-payjoin

- witness_utxo fee issue: path decided, team brief written, waiting on the human to open the team discussion (see kb/topics/witness-utxo-fee-issue.md). Panes that stay until the human is done with the reports: synthesis wS:p2Y, fundamentals wS:p2R, discovery-a to e wS:p2S to p2X, the fee fix worker wS:pR, all idle; worktree ~/repos/rust-payjoin-untrusted-inputs [checked: agent list 2026-11-07 05:31].
- Native FFI builds PR 2023: merged upstream 2026-11-06 [checked: gh, previous state]. Nothing waits on it here; the PR 1996 vs 1815 pane wS:pB is idle and can be released.
- Receiver settlement PR 1971: merged 2026-11-05; follow-ups in open issue 2034 [checked: gh, previous state]. Optional for the human: post the comment adding the one follow-up the issue misses (the SessionStatus rustdoc caveat), draft at ~/review-drafts/payjoin-1971/draft-issue-2034-completed.md.
- The OpenCode security audit pane wS:p11 is idle; its conclusions are on the project page; it can be released.

## ldk-node

- Three research reports under ~/review-drafts/ldk-research/, summarized on the project page; not yet reviewed by super.

## harness

- Deployed 2026-11-07 about 01:15 UTC from the laptop: the cron block (audit 02:17, interop 04:45, dream 05:30 UTC), the memory scripts and the watcher run from the image; one watcher process under the entrypoint, no hand-run copy [checked: crontab, /usr/local/bin timestamps, process list]. The super agent's socket file is rewritten on every turn since the resume timeout found at the deploy [checked: hub main log]. Nothing in flight; the harness workspace is closed.
- Scheduled jobs run in workspace wY, labelled scheduled, one pane per job [checked: pane list].
- Open with the human: cleanup rules for worktrees, sessions and temporary files (board item harness-cleanup), and the audit repo backup (above).
- Workspaces by label: rust-payjoin wS, payjoin-audit wT (one leftover shell pane still labelled for the 2026-11-05 hand run), super wW, scheduled wY; harness gets one per task and closes with it [checked: workspace and pane lists 2026-11-07 05:31].

## super

- Dreaming: the first scheduled pass ran tonight at 05:30 UTC with the 80 turn cap; its branch waits for the super agent's review [checked: this pass].
