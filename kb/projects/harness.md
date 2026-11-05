# harness

Hub repo: ~/repos/harness.git (main). Worktrees ~/repos/harness-<slug>. Holds the workhorse image, the dashboard (infra/workhorse/dashboard/server.py), super-watch, super-sync, agents-refresh, and this super agent's instruction files (SUPER.md, etc/super-settings.json). Progress is recorded in docs/progress.md.

## Super agent first run (2026-11-05, branch super-record, worker wV:p1)

Hub main fast-forwarded to 44f287d8b855a304b4ec0985ef4693dde4d5470e (four commits over 6d98af4): show prompts taken mid-turn (queued_command records), strip the pasted_content wrapper, 44 px tap targets, progress record. Served dashboard is on it. Report was at /tmp/handoff/super/out/REPORT.md (tmp, may vanish).

What runs on the box as stopgaps until the image deploy: ~/super made by hand from the hub's agents-refresh; qmd 2.8.3 under ~/.local; ~/super/kb is a git init with origin https://github.com/spacebear21/super-kb.git (repo does not exist yet, no PAT, so nothing is pushed); super-watch hand-run in pane wV:p2; super-sync not running.

Dashboard API: GET /api/super gives my pane, status and transcript; POST /api/super/say types into my pane (limits: 4000 chars, printable, X-Dashboard header, 409 when no super agent or on a dialog).

## Update 02:30

The human made ~/super the git repository (origin spacebear21/super.git) with one initial commit holding the kb files unchanged; the old kb/.git is gone. The new super-watch from main wakes the super agent only on inbox notes and dialogs. Scratch tests in ~/scratch-super-check-3 (a dialog and a note) are to be left alone.

## Steps left for the laptop (human)

1. Deploy the image with infra/scripts/workhorse-deploy.sh. Afterwards stop the hand-run super-watch in pane wV:p2 (ctrl+c), since the entrypoint runs its own.
2. Create private repo spacebear21/super-kb (empty, Actions disabled); mint a fine-grained PAT with Contents read and write on that repo only; set it with workhorse-deploy.sh --set-secrets. The first super-sync pass pushes the kb commits.
3. /login in the super session before the login expires (warned on 2026-11-05: one day left).
4. /rc in the super session if Remote Control is wanted; consider remoteControlAtStartup in ~/super/.claude/settings.json or etc/super-settings.json.
5. Open the dashboard on the phone; home is the conversation.

## Open note from the worker

A second watcher with SUPER_DIR pointed elsewhere treats the real super pane as a worker, and the two watchers loop through each other's wake-ups. Recorded in docs/progress.md as open.
