# memory: dreaming pass and transcript index

Branch `memory` in ~/repos/harness-memory (worktree of the harness hub), head `e75f670926b8240057786b9e7ce1f04d13bf4a90`, on main e38e308. Not pushed, not merged.

Commits:
- ac8acbd Add transcripts-index for agent session search
- f0e324e Add super-dream for the nightly dreaming pass
- 939a428 Schedule super-dream daily at 05:30 UTC
- fbdc23b Schedule the interop matrix daily at 04:45 UTC
- e75f670 Document the dreaming pass and the new tasks

Numbers:
- transcripts-index, first real run: 3055 sessions (86 Claude Code, 205 Codex, 2764 OpenCode), 3049 written, 106 MB, 13 s to render plus 35 s for qmd to add and index the collection, 49 s total. Rerun: 3 s. Collection `transcripts` is live in qmd now.
- Trial pass, super-dream --max-turns 40: Fable 5.1 (per its result), 41 turns, 9.5 min, $6.48, 4 commits. It hit the turn cap before writing its note, so the script wrote the fallback ~/super/inbox/dream.md. Trial branch: `dream/2026-11-06` (head 473220a), worktree ~/super-dream. Not merged. Note it conflicts with current ~/super main, since you committed to kb while it ran.

What I need: the human to push `memory` to the hub's main and deploy. Until the deploy, super-dream and transcripts-index are not on PATH and the 05:30 cron firing fails with command not found.

Decisions beyond the brief:
- Every hook is off in the dream session. A headless claude -p runs the super repo's SessionStart hook with a socket of its own, which would have pointed super-watch at the dream session. Verified: the socket file was untouched by the trial.
- Four hour timeout per pass. Lock against overlapping passes.
- The schedule requires a repo per task, so super-dream's entry names spacebear21/super at ~/super (GH_TOKEN can read it).
- payjoin-interop is live, not disabled: schedule.toml has no enabled field. Until bin/run-interop.sh is on payjoin-audit master, each firing logs `payjoin-interop exit 127` in ~/.cache/payjoin-audit/interop/cron.log.

What dreaming.md asks that the harness does not fully honour, or that needs a decision:
- Token Factory delegation sends transcripts to the TFF operator. The runbook says to keep private material away from TFF. The trial delegated nothing, but dreaming.md tells it to. Human's call.
- The trial checked claims by grepping raw JSONL, not through qmd -c transcripts. It also read herdr agent list and workspace list, which dreaming.md does not clearly forbid.
- The global ~/.claude/CLAUDE.md applies to the dream session too, and it tells workers to hand off to ~/super/inbox/<branch>.md and to load the herdr skill. The trial didn't follow it, but nothing prevents a pass from writing inbox/dream/<date>.md. If that matters, dreaming.md (or the super CLAUDE.md) could say the hand-off rule does not apply.
- Subagent transcripts and OpenCode child sessions are not indexed, so "every session" means top-level sessions.
- Dream branches live only on the box. super-sync pushes main, so an unmerged branch is lost on a rebuild.
- Cost: 40 turns cost $6.48, so the default of 150 could cost around $25 a night. Worth confirming the cap.
