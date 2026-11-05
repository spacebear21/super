# harness

The workhorse image, its entrypoint, the dashboard, super-watch, super-sync and the seeds of this agent's own instructions. The hub is the bare repo ~/repos/harness.git, worktrees ~/repos/harness-<slug>; docs/runbook.md in the repo explains how everything is wired and why, so nothing of that is repeated here.

## Decisions taken out of band

- **Scheduled tasks live in this repo** (human, 2026-11-05). A committed schedule of every cron task, with the repositories each needs, so the schedule is in history and comes back whole on another machine, and the image pulls those repositories in. The payjoin audit's own install script and image notes were the thing this replaces: a crontab installed by hand is lost on every rebuild and nothing in the harness knew the task existed.
- **A deleted inbox note is a handled note** (human, 2026-11-05). super-watch drops queued note events whose file is gone before it prompts. The alternative, waiting until a note's modification time has been stable before announcing it, was rejected: the human is "not a fan of the delay". A rewritten note that still exists keeps counting as new.
- **Wake the super agent only for notes and dialogs, never for a worker finishing a turn**, because every wake-up costs a turn of context and most turn ends need nothing; when the super agent wants an answer it waits for it itself. Settled before my memory starts; the runbook carries the reasoning.
- **The super agent sits alone in its own workspace** and belongs to no project; harness work goes in its own workspace like any project.
- **A reviewed fix the human asked for may be landed in hub main and swapped into the root filesystem by hand** (done for super-watch) without waiting for an image deploy; the deploy carries it permanently. The root filesystem is wiped on rebuild, so the hub copy is the one that matters.

## Learned

- Claude Code holds text typed into a pane mid-turn until the turn ends, so a watcher prompt can arrive after the thing it announces is gone. Any mechanism that types into the super pane has to assume its message may be stale by the time it is read.
- A second super-watch with SUPER_DIR pointed elsewhere treats the real super pane as a worker, and the two watchers loop through each other's wake-ups. Test watchers need their own super agent or none.
- The dashboard worker's scratch agents and notes (names starting scratch-super-check) are test fixtures; answering one's dialog skewed its test.
- Right after `herdr pane run`, a process search for the command matches a short-lived wrapper first; the real process is the one that is still there ten seconds later. Watchers keyed on the wrong pid fire at once.
- A worker that touches its hand-off note up seconds after writing it makes a second event; the fix above makes that harmless, but a note that is deleted and rewritten before the prompt goes out is still named twice in one line.
- A long prompt (about 900 words) typed into a fresh OpenCode pane through Herdr can land in an empty input line and never submit, while the same prompt into the tab's root pane works. Writing the brief to a file and prompting with one short line that points at it works every time. Also: four same-direction splits in one tab make OpenCode panes too narrow to read; split alternately or use tabs.
- Agents sharing one output directory will delete each other's files: a fast OpenCode model, re-reading its brief after a second prompt, saw reports it had not written, decided it must have created them by mistake, and removed them under the global rule to make no unrelated changes. When several agents write to one place, say in each brief that other files there belong to other agents and must never be touched, or give each agent its own directory and merge afterwards. Snapshot shared outputs as they land.
- super-watch types into the super pane whenever the super agent is not on a dialog, including while the human is typing there, which splices the wake-up line into the human's message (happened 2026-11-05 19:20). Fix in flight: hold the wake-up while the input line has pending text.
