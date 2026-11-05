# Decisions

- 2026-11-05: Agents whose cwd starts with ~/scratch-super-check are test fixtures of the harness dashboard worker (harness-headless-count, wV:p1), not workers. Never answer their dialogs or prompt them. Learned after I answered one's trust dialog and skewed the worker's test.
- 2026-11-05: The real super-watch is hand-run in pane wV:p2 (workspace wV, tab t1) until the image deploy; after the deploy the entrypoint runs its own and the hand-run one is stopped. If it dies before then, restart it with `herdr pane run wV:p2 super-watch`. A test watcher pointed at another SUPER_DIR treats the super pane as a worker and loops with the real one.
