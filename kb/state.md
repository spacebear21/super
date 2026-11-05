# State

Last rewritten: 2026-11-05 02:21

## payjoin-audit (nightly multi model audit of Payjoin implementations)

- First limited nightly done (02:09). master at 30427e5, cron live at 02:17 UTC. 18 drafts in reports/drafts/ await human review before anything is sent; see projects/payjoin-audit.md for the ranked ones. The wT:p1 worker is finished and asked the human whether to drop the OpenCode Zen free tier models from the pool. The full nightly runs from cron and commits its own results.
- Pane wT:p3 (tab '1', shows as opencode, codex or claude) is the shell running bin/audit; Herdr flips its status as the pipeline spawns codex calls, so 'opencode in 1 is now idle' wake-ups are noise while the run log keeps advancing.
- Headless refute workers inherit the global hand-off rule and write notes to ~/super/inbox/ named after the audited repo's branch (first seen: master.md, 00:32). Their result is already in ~/.cache/payjoin-audit/calls/<run>/*.out.json. Treat such notes as noise: read, confirm the match, delete.

## rust-payjoin

- PR #2016 native builds: phase2 (wS:pP) finished native-build-6 at bb0a47f9 with report ~/repos/native-build-6-REPORT.md; The open PR is #2023 (head spacebear21:native-build at 9c01ab79), not #2016. Recommendation to the human: force push native-build-6 (bb0a47f9) to spacebear21:native-build and paste the PR description from ~/repos/native-build-6-PR-2023.md. The remote branch holds nothing beyond the rebased nine commits. Waiting on the human. Details in projects/rust-payjoin.md.
- Other wS workers not yet reviewed by super: fallback_tx exposure in monitor typestate (wS:pH, done), BIP-78 witness_utxo fee gap (wS:pR, idle), security audit of BIP78/BIP77 (wS:p11, opencode, said "Nothing needed").
- rust-payjoin-red-team: the wT:p1 worker that built payjoin-audit is done (see payjoin-audit above).

## ldk-node

- Three opencode workers done in tab wS:t8: ChannelMonitor output tracking, funding candidate conflicts, Node wallet transaction classification. Not yet reviewed by super.

## harness

- Super agent first run verified and recorded by the worker in wV:p1 (branch super-record, worktree ~/repos/harness-super, hub main at 44f287d). Worker is done; its scratch fixtures are gone. Laptop steps for the human are in projects/harness.md: image deploy, super-kb repo and PAT, /login, /rc. The kb has no remote copy until step 2.