# Cleanup rules for the harness

## What it is
You said on 2026-11-07, after the audit's OpenCode database lock storm: "in general i do think we need to instate some cleanup rules for the harness, especially worktrees, stale agent sessions, and large temporary files". Only OpenCode keeps a shared SQLite database; Claude Code and Codex write one append only JSONL file per session, which only grows.

## Where it stands
Inventory taken that night: the home volume is at 188 GB of 394 GB. Worktrees 84 GB (51 across harness, payjoin-audit and rust-payjoin, 25 already merged, 13 Rust target directories, one worktree alone 23 GB). Audit repo clones 48 GB (386 clones, one educational content repo 19 GB). OpenCode database 2.1 GB plus snapshots, log and tool output. Claude sessions 274 MB (149, 44 untouched for a week), Codex sessions 154 MB (206). Other caches 12 GB, /tmp 6.8 GB. Nothing has been built.

## What is asked
Yes, or changed thresholds, to four rules run as a daily harness job before the audit, with a report in the morning note:
1. Worktrees: remove one whose branch is merged into its hub's main and idle for 48 hours, prune its branch; never one hosting a live Herdr pane; Rust target directories after 7 days idle.
2. Agent sessions: Claude and Codex session files indexed into the transcripts collection first, compressed after 7 days, deleted after 30, except sessions a live pane can resume. OpenCode: vacuum weekly, drop snapshots and tool output older than 7 days, rotate the log at 50 MB.
3. Temporary files: the audit's call records, scratchpads and /tmp older than 7 days; the interop download cache kept; audit clones made shallow single branch with build directories removed after each run.
4. Guard rail: nothing deleted while the audit or interop job runs; the job logs what it removed and how much it freed; dry run for the first week.

Once you answer, an Opus worker builds it on the harness.
