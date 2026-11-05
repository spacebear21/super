# Decisions

- 2026-11-05: Agents and notes from ~/scratch-super-check* (any suffix) are test fixtures of the harness dashboard worker (harness-headless-count, wV:p1), not workers. Never answer their dialogs or prompt them. Learned after I answered one's trust dialog and skewed the worker's test.
- 2026-11-05 02:30: ~/super is the git repository (origin https://github.com/spacebear21/super.git); kb/.git is gone and the old kb history with it. Commit from ~/super. The new super-watch wakes me only on inbox notes and dialogs, not on worker turns.
- 2026-11-05: The real super-watch is hand-run in pane wV:p2 (workspace wV, tab t1) until the image deploy; after the deploy the entrypoint runs its own and the hand-run one is stopped. If it dies before then, restart it with `herdr pane run wV:p2 super-watch`. A test watcher pointed at another SUPER_DIR treats the super pane as a worker and loops with the real one.
- 2026-11-05: The human dropped the OpenCode Zen free tier models from the payjoin-audit pool (commit e27c3cc); the opencode backend uses the Token Factory tff/ family only. Their call, not the worker's.
- 2026-11-05 ~16:00: The human wants censored (faster) models to try audit and refute first, with a fallback to an uncensored model on refusal, and refusal data collected to adjust later. Stage barriers are a minor concern; pipeline the audit-to-finding step if simple, leave the rest.
- 2026-11-05 ~16:40: super-watch fix: drop queued note events for files that no longer exist at prompt time. The human does not want a debounce or delay on announcing notes.
