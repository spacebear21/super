# Review of payjoin receiver settlement PR (payjoin/rust-payjoin#1971)

## What I did

Re-reviewed the latest push, head 71f0a1b08cc19a7d23c5ad47fbb55d27fd5db989 (fetched locally as branch pr-1971-nov5, review worktree ~/repos/rust-payjoin-review-1971). The author addressed the review: ResolvedBy / resolved_by() with #[non_exhaustive], Resolution now carries the tx and close reclassifies against the closing state (with a test), the reordered-outputs test is back, the PR description and the inaccurate doc and commit claims are fixed. Everything passes locally: payjoin lib (309), doc, integration (16), payjoin-cli unit and e2e (8 and 9), Python bindings (21), clippy -D warnings, fmt.

## What I need

Post a reply in the Resolution review thread: https://github.com/payjoin/rust-payjoin/pull/1971#discussion_r4461064108

xstoicunicornx asked to be walked through the stale classification flow, since reclassifying on the same object gives the same answer. The draft explains that the resolution and the closed receiver are different objects (his own new test is the flow), and gives the realistic scenario and why it is an edge case.

Draft: /home/agent/review-drafts/payjoin-1971/draft-reply-stale-flow.md

Remaining non-blocking items, for your call whether to raise them before ACK: no caveat on SessionStatus / status() that Completed says nothing about payment; close docs do not say what it records; cancel --no-broadcast still prints "Broadcast the original transaction manually"; no FFI migration note for the removed check_for_transaction and proposal_txid_is_stable.
