# Prompts sent to workers


- 2026-11-05 00:41 wV:p4 (scratch): answered folder-trust dialog for empty ~/scratch-super-check with Yes (keys: down, enter). No prompt sent.
- 2026-11-05 00:46 wV:p1 (harness-headless-count): told it that its ctrl+c into wV:p2 killed the real super-watch, which I restarted as pid 373014; that its scratch test watcher loops with the real one via my pane; and that I will leave scratch-super-check* agents alone.
- 2026-11-05 02:20 wS:pB (PR 1996 vs 1815 pane, supervising phase2): told it #2016 is closed, superseded by #2023 whose head is spacebear21:native-build at 9c01ab79, so the force push target changes; asked it to reword the PR description.
- 2026-11-05 02:55 wT:p3 (shell): ran bin/run.sh for a full payjoin audit at the human's request
- 2026-11-05 15:00 wT:p1 (claude): brief to parallelize payjoin-audit on branch parallel (worktree ~/repos/payjoin-audit-parallel): parallel process_finding, raise concurrency, spread load off the two uncensored models, back off on 429s, race discovery with shorter timeout, flock in run.sh
