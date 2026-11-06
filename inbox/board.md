# board: the super agent's board on the phone dashboard

Branch `board` in ~/repos/harness-board (worktree of the harness hub), head `743c3ee2e81137dfe486553a3840a5f0dbfb39a3`, five commits on main e38e308. Not pushed.

## What I built

- 829ddf5 Serve the super agent's board at /api/board. Reads `~/super/board.json` (path is `SUPER_DIR/board.json`, overridable with `BOARD_FILE`, like `SCHEDULE_FILE`). It checks schema 1 and returns `{updated, items, stale, error}`, always HTTP 200. The server sorts and filters: needs human first, then priority, then status order, done last. Ties go by `since`, then `id`. Reads are cached for 5 s. `stale` (updated over 12 h ago) is computed on every request. Strings are cleaned with the existing `plain()`. `needs` keeps its line breaks.
- 9790eec Home page: a Priorities section above the conversation, polled every 10 s.
- f9323b7 Project view: the same rows filtered to the project, above its tasks.
- 9d69689 `infra/workhorse/dashboard/test_board.py`, 14 unittest cases, standard library only.
- 743c3ee Runbook: a "Board" paragraph in Phone dashboard.

## Decisions you may want to check

- One bad item makes the whole file an error with no items. The error names the item and the field. I chose this so a dropped item could never hide a needs-human one. The other option is to drop bad items and show the rest.
- Day-old done rule: the schema has no "done at" time, so I use `since` as the day it was marked done. A done item stays while the board's `updated` date is at most one day after `since`. That means it is visible on the day it was marked and the next day. A stricter "same day only" would make an item marked at 23:50 disappear minutes later.
- `needs` is shown only for waiting-human and blocked items. It is required for those and ignored on any other status. `worker` is validated and returned, but the page does not show it, since the spec's row list leaves it out.
- Project matching: the board names a project by registry or repo name, while the page keys projects as owner/repo. A row matches on the full key or on its repo part.
- Status chip words: waiting-human shows as "needs you", waiting-worker as "waiting on worker". The other statuses use their own names.
- Duplicate ids, `updated` not in UTC, priority not 1 to 3, and files over 512 KB or 200 items are all invalid.

## How I tested it

- `python3 -m unittest` in infra/workhorse/dashboard: 14 tests, all pass. They cover a valid file, a missing file, invalid JSON, non UTF-8, wrong schema, a bad status, other bad fields, the sort order, the stale flag (including from a cached read), the cache TTL, and the done rule.
- I ran a hand-run copy on spare ports against the live Herdr with `BOARD_FILE` pointing at a sample board, called `/api/board` with curl, and took Playwright screenshots of home and a project view at 390x844 in light and dark. There were no page errors. To get headless Chromium running I installed its system libraries with `sudo npx playwright install-deps chromium`.

## What got in the way in the existing page

- Home has no workspaces section. It is the super agent's conversation, and workspaces live under `All`. I put Priorities above the conversation in its own scroll area, capped at 42% of the screen height so the chat keeps its room. If you meant `All agents`, it is a small move.
- The dashboard's `.chip` is a 44 px tap-target button for dialog options, not a status label. I reused its border and radius as a smaller non-interactive `.chip.tag`.
- An existing bug I did not fix because it is out of scope: in `renderProject` (index.html), the local `const tasks = new Map()` shadows the global `tasks`. So `tasks[k]` is always undefined, and the scheduled task log rows (`<name> log`) never appear in a project view. Renaming the local variable fixes it.
- AGENTS.md asks for completed work to be recorded in docs/progress.md as its own commit. The task limited me to the dashboard and runbook, so progress.md is not updated. Say if you want that commit.
- `dashboard-refresh` exports the whole dashboard directory, so test_board.py ships to the served copy too. It is harmless, because the server only serves its static allowlist.

## What I need

A review and a push of `board` to main (`git push workhorse main` makes it live), plus answers on the decisions above if any should change.
