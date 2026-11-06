# pr-review: pull request review stage

Branch `pr-review` in ~/repos/payjoin-audit-pr-review, off master bc6bd9b.
Head: 964de8c77bc7a48d61d85e21448d5175a9f52ac6. Eleven commits, each a standalone step: design doc,
config and schema, scan plumbing, selection, checkout, review, transitions,
drafts, pipeline wiring, two selection refinements. Full suite (65 tests)
and contrib/lint.sh pass. Nothing merged, no model called, the cache was
not written; the only live use was `bin/audit pr-plan` (GitHub reads).

## What it does

docs/pr-review.md has the design. Nightly, after the rechecks: list open
pull requests of active projects and of prerelease, draft and poc watch
projects; pick those in open_prs, whose title names a Payjoin keyword, or
whose changed files fall under code_paths or name a keyword (bounded
files lookups); check each head out as a detached worktree beside the
clone; audit the diff per touched area with prompts/pr-review.md; run
introduced findings through dedupe, refute and tiebreak with refuters
reading the worktree; keep pre-existing ones as hints in pr-hints.jsonl;
draft accepted ones as review comments under reports/drafts/pr/; recheck
when the PR moves, promote or fix when it merges, invalidate when it
closes. `bin/audit pr-plan` lists tonight's pick. `--no-pr-review`
skips the stage.

## Decisions changed from the brief, and why

- PR finding fingerprints carry `#pr-<number>` until merge, so a PR
  finding never folds into a mainline finding or another PR's before the
  merge decides; on merge the suffix is dropped or the finding becomes a
  duplicate of the mainline one.
- What was reviewed lives on the project as `pr_reviews` (brief did not
  say where the head memory lives).
- Keywords match titles and changed paths, not bodies: the live listing
  showed bullbitcoin-mobile's PR template making unrelated PRs match.
- The shared cap is round robin across projects within each group: the
  first listing gave all 20 slots to rust-payjoin and bullbitcoin-mobile.
- Files lookups capped at 30 per project per night, and a listed PR
  missing from the 100 PR page is fetched on its own (bitcoin-core's
  #32617 was invisible otherwise).
- Pre-existing hints are written to the run dir and the summary, not fed
  into the mainline audit prompt; that wiring is left for later.

## Not settled

- bullbitcoin-mobile's code_paths include whole feature directories, so
  many unrelated PRs match by code_paths; that is registry data to tighten.
- open_prs entries on forks (payjoin/liana, evanlinjin/bdk-tx) are skipped
  with a log; fetching from a fork remote is not implemented.
- The review stage has never run against a model. The refute note, the
  pr-review prompt and the recheck of moved heads are tested with fakes.

## Merge advice

Wait for a human look before a nightly run. The code paths that touch
the registry are tested, but the first real run will fetch up to 20 PR
heads and spend audit calls on them; a first run with
`--cap` low via `caps.pr_reviews_per_run` set to 5, and a read of
reports/drafts/pr/ afterwards, is the safer start. Stopped here because
the usage limit was reached.
