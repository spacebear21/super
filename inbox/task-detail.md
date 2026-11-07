# task-detail: task pages with detail and answers, done

Branch `task-detail` in ~/repos/harness-task-detail, head `af2f2d433e660e71f06ae8dd93e255326f8bb052`, seven commits on main 4a05365. Not pushed.

- 822164b Serve one board task with its detail file (GET /api/task/<id>)
- d39b362 Factor the super agent prompt out of Send (no behaviour change, split out so the reply shares Send's code)
- 4be2c87 Answer a board task from its page (POST /api/task/<id>/reply, prefix `[task <id>: <title>] `)
- f98b019 Open a task from Tasks and answer it in place (page, #/t/<id>, safe Markdown renderer)
- 620703f Test the task and reply routes (30 tests pass)
- 23ec7e5 Describe task pages and answers in the runbook
- af2f2d4 Record the task pages and answers

Screenshots, 390x844, against live Herdr and ~/super/board.json:
- ~/shots/dashboard-task-detail/task-light.png
- ~/shots/dashboard-task-detail/task-dark.png
- ~/shots/dashboard-task-detail/task-light-not-sent.png and task-dark-not-sent.png (the failed send line, against a faked 409)

What got in the way:

1. Two test answers reached you. The first browser run sent "Go with option two" and "again", tagged `[task untrusted-inputs: ...]`, to your pane. The page's service worker made the fetch, so Playwright's request interception never saw it, and the hand-run copy had the real Herdr. I wrote task-detail-test-replies.md to the inbox right away. Neither message came from the human, and no decision was made on untrusted-inputs. The rerun blocked service workers and gave the copy a Herdr wrapper that refuses everything except read-only commands. The runbook's development section now says to do both.
2. The brief said to use "the fakes the existing tests use for Herdr", but test_board.py had none. I added `FakeHerdr` and a real server on a free port, so the gates run as they do live.

Choices worth a look:
- A `detail` path that is wrong in substance (outside ~/super, not .md, missing) shows a one-line `detail_error` on that task's page and leaves the board alone. A `detail` that is not a string fails the whole board, as any other bad field does.
- A file over 64 KB is cut at a line end and flagged, not refused.
- Back from a task opened from a project view returns to the project, following history like every other view. From Tasks, or from a direct link, it returns to Tasks.
- The Markdown subset is what the brief listed. Numbered lists and code fences read as plain paragraphs. Say if your tasks/*.md files use them and I will add them.

Needs: a push of `task-detail` to the hub's main to go live (merges cleanly on 4a05365).
