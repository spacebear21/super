# Cake PR 3363 review comment

## What it is
Cake Wallet's pull request 3363 upgrades to payjoin 1.0.0. You asked on 2026-11-06 for pre merge review of PRs that touch payjoin logic. A worker reviewed it by hand before the review stage existed.

## What it found
- Regression: the v1 send path is gone, and payjoin 1.0.0 panics on v1 endpoints, so a v1 URI crashes the sender.
- Regression: the receiver honours the sender's minfeerate up to 10,000 sat/vB, so a hostile sender can make the receiver pay.
- Gap: custom relay and directory URLs accept http://.

The new review stage later ran on the same PR as its live trial and found the v1 panic too, plus four more defects (one operator can hold both relay and directory roles in the new default pools, a 10 second request timeout aborts every empty mailbox long poll, two sends to one endpoint interleave on a session row, a backfill marks a receiver session successful when the fallback arrives). Those four are drafted when the stage runs for real; they can follow as a second comment.

## What is asked
Post the comment. The text is at the end of `reports/triage/2026-11-06-cake-pr-3363.md` on payjoin-audit master, written for GitHub (one paragraph per line, no attack detail that is not already public in the PR).
