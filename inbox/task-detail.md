# task-detail: docs brought in line with the renderer, done

Branch `task-detail` in ~/repos/harness-task-detail, head `bed6fd0b134cba47ebaf0796398e9c921361cd1c`. It is one commit on eb51431. Not pushed.

- bed6fd0 Document numbered lists and fences in task pages

docs/runbook.md: in the Tasks paragraph, the Markdown subset now lists bullet and numbered lists and fenced code blocks.

docs/progress.md: in the task pages record, the renderer bullet now says numbered lists and fences render, escaped like the rest, and why they were added. The verification line now says 32 tests, including the two Node renderer tests. That count was stale too, so it went in the same commit.

No code changed.

Needs: a push of `task-detail` to the hub's main to go live.
