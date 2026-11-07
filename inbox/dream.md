# Dream pass 2026-11-07

Branch dream/2026-11-07, head 37e3595, six commits on top of 31e0f96:

- 88ec8d4 Mark the day's decisions against the transcript: same-job review (now with the human's words of 23:40), cap 80, out-of-band clearing; the hand-run watcher line folded into its outcome (one watcher under the entrypoint since the deploy).
- f915548 File the human's cleanup-rules direction and board it: the 03:13 ask and your 03:19 proposal were only in the conversation; now a decision, a waiting-human item harness-cleanup, and tasks/harness-cleanup.md.
- f7eb5bf Rewrite state for the morning: interop merged and run from cron, audit cron run alive and refuting at 05:35, harness after the deploy, scheduled workspace wY, a super section.
- bd27cf8 Drop done board items older than a day (pr-review-stage, native-ffi-2023, dashboard-board).
- 7813d21 Move kb/projects/payjoin-interop.md to kb/topics/interop-matrix.md with a status line; payjoin-audit.md links it from a new topic index. Content unchanged.
- 37e3595 Source marks and stale lines on the audit and harness pages: dropped README repeats and the "18 drafts await review" line, corrected "still waiting on the rest of the sheet", marked the OpenCode isolation, reply feedback, socket file and service worker claims.

Checked and found right, no edit: the 02:17 origin (15a8a837, "around 02:15 UTC" on 2026-11-04, 02:17 in config), the interop numbers (64/37/53, 44 cells, 24 min), the hand run and cron run overwriting one file (76a79f9 and ff7591a), the 2 GB database and 24 writers, the 9.1 s hook cancel.

Could not settle:
- Whether the human will change the audit's 02:17 start; they asked why at 01:53 and said nothing after your answer. A question on the board would settle it if you want it settled.
- The interop cron run ran on a dirty harness tree (reports/interop/2026-11-07.json says harness.dirty true at 04:51). I did not look at what was dirty; a glance at git status in ~/repos/payjoin-audit says whether that matters for provenance.
- Pane wT:p3 still carries the label "Running payjoin audit (parallel, resumed 2026-11-05)" while the real runs happen in wY. The human's workspace rule says a workspace shows only in-flight work; releasing it is yours to do, I touch no pane.

On how you write: the two things that were missing today were both questions you asked the human in chat and never put on the board (cleanup rules; whether 02:17 should move). The board rule in CLAUDE.md already covers it; the habit to build is "asked the human something" means "board item with a task page" in the same turn. Also kb/state.md was last rewritten by the previous dream pass, not by you, through a day with four status changes; a rewrite at each change is cheaper than a reconstruction at night. Pages still to reshape when there is time: payjoin-audit.md holds three report-sized sections (the 2026-11-05 run's hours, the 2026-11-06 token accounting, the PR review trial) that belong on a topic page pointing at the call records.
