# payjoin-audit: branch parallel, ready to merge

Worktree ~/repos/payjoin-audit-parallel, branch parallel off master dd270ad, head bc00719. contrib/lint.sh --check is clean (ruff, shfmt, shellcheck, codespell, 21 tests, registry check). No run was started.

Commits, oldest first:

- a2db194 Give every finding its own call labels. Found while making the pipeline parallel: dedupe and refute labels were project+class(+symbol), and the runner reuses any logged answer under the same label within a run, so a second finding of the same project and class got the first one's dedupe verdict. Labels now carry a 6 character hash of the finding. Audit, triage and discover labels are unchanged.
- 4063169 Process findings concurrently after the audit tasks. Raw findings are grouped by (project, class); groups run through runner.map, each group in order. Explore verifies proposed classes in parallel and sends its findings down the same path. Recheck, deps, maturity and triage already mapped their model calls; no change needed there.
- 245fcda Back off on rate limits instead of counting failures. Errors matching 429, rate limit, too many requests, overloaded, quota, capacity, 502/503/504, temporarily unavailable, ECONNRESET are retried on the same model after 20 s, 40 s, 80 s (retry.backoff_s, retry.transient_max 3) and do not count toward disabling. Every 3 such answers (retry.throttle_after) lower that model's concurrent slots by one, floor one.
- 759e9e1 Spread calls across the pool and raise concurrency. Candidates are reordered by free capacity before each ask: preferred (uncensored) models are still used while they have a slot, then load moves to the next model instead of queueing. concurrency.total 8 to 24, per_model 2 to 6.
- de1d7ba Race every discovery call and cap it at 900 seconds. runner.ask gained race=True; discovery uses it; timeouts_s.discover 900.
- bc00719 Refuse to start a second run while one is running. bin/run.sh takes flock -n on ~/.cache/payjoin-audit/run.lock and exits 1 with a message when held. Verified with two shells.

Resume: runner._cached is unchanged. Starting with --name 2026-11-05 reuses the 80 audit answers (154 logged audit calls incl. raced losers), the 100 triage answers and the discovery answers already under calls/2026-11-05. Dedupe and refute answers from the killed run are not reused because their labels changed (that was the bug); the ~50 findings that run already recorded are caught by the fingerprint check without a model call.

Not settled, your call:

1. Concurrency numbers are a guess. trytokenfactory.dev refused connections and its pricing page returns 401; the gateway's /v1/models response carries no x-ratelimit headers. 24 total and 6 per model as instructed; tune from the model usage table after the first run. If the gateway throttles, the throttle logic will shrink per-model slots on its own.
2. The transient error regex is matched against the error text opencode emits; the first run produced no real 429 to test against, so the pattern is from the usual wording. If a rate limited call shows up in a log as "failed:" rather than "rate limited, retry", send me the text and I will add it.
3. Residual dedupe race: two findings of the same project under different classes that describe one defect can still both pass as new when their groups run at the same time (same as before, but now more likely). Grouping by project alone would close it at the cost of serialising rust-payjoin's findings. I kept (project, class) as specified.
4. README still says the primary pool is the tff and opencode providers; the opencode free tier was removed on master in e27c3cc. One line, left alone to keep this branch on topic.
