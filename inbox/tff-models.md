# tff-models: two withdrawn Token Factory models removed

Branch `tff-models` in `~/repos/harness-tff-models`, one commit, fast-forwarded into hub main:

- e38e308d903ec98d57c6171605ab01d1722bfcd7 Drop two withdrawn Token Factory models

Removes `kimi-k3` and `muse-spark-open` from `infra/workhorse/etc/opencode.json`, leaving the 12 the gateway lists. That file is the only Token Factory model list in the repo; the two mentions in docs/progress.md are historical records of what the gateway listed then and were left alone. JSON parses and git diff --check passes; the repo has no other lint for this file.

Worth knowing: the merge was a local ref update in the hub, so the post-receive hook did not run (nothing it refreshes reads this file anyway), and the config is baked into the image at /etc/opencode/opencode.json, so the live box keeps offering the two models until the next deploy. Nothing else was touched.
