# payjoin-audit

Repo: ~/repos/payjoin-audit, branch master. Nightly multi model audit of Payjoin implementations. README explains the pipeline, registry/SCHEMA.md the data model. Audited projects are read only clones under ~/.cache/payjoin-audit/repos/. Per call prompts and outputs land in ~/.cache/payjoin-audit/calls/<run>/.

## Standing rules from the repo's CLAUDE.md

- Nothing in the repo sends a report. Drafts under reports/drafts/ are reviewed and sent by a human.
- The registry is the source of truth; terminal finding statuses and retired classes are never reopened from code.
- bin/audit runs under python3 -I on the standard library only.
- Prompts live in prompts/, never in code. Run contrib/lint.sh before committing.

## First nightly (2026-11-05, worker wT:p1, master at 30427e510c1904acec0024248573aec5d85e4b72)

Seed adopted: 321 projects, 313 issue classes. Cron installed: 17 02 * * * bin/nightly.sh, log at ~/.cache/payjoin-audit/cron.log. A limited nightly (8 audit tasks, no discovery) ran by hand and produced 18 drafts under reports/drafts/ (dated 2026-11-04) awaiting human review: bdk-cli F-0001, F-0010 to F-0013; bitcoinjs-payjoin-client F-0002 to F-0009; bitmask-core F-0014; ltblightning payjoin-ffi F-0016, F-0017; payjoin-flutter F-0018, F-0019. Highest ranked: BitMask critical (pins payjoin 0.8.2, lacks the sender output-validation fix), two highs for bitcoinjs-payjoin-client (cleartext endpoints accepted by default; no way for a receiver's pjos=0 to disable output substitution). F-0005 and F-0007 describe the same unstripped PSBT fields, one can be dismissed. Bark findings were refuted as "not a Payjoin implementation" with model dissent on four; the next run lists such cases under "Refuted with dissent". Cake Wallet and Bull Bitcoin Mobile pin the Flutter plugin by git ref, which the dependency stage cannot compare yet.

Fixes committed after the run: refuters get the registered project role, contested refutations appear in the summary, dependency pins skip own package and shadowed wrappers, check flags projects sharing a repo (BitMask duplicate merged), dedupe compares across classes, drafts omit empty fields.

## Findings worth remembering

- bitcoinjs-payjoin-client at 1.0.1 (b15b03f6d9552c278a867b2b0d61f08d4e319311): original PSBT cleanup keeps unknownKeyVals on global, input and output maps, so proprietary wallet metadata reaches the receiver. BIP78 says the original PSBT must not include fields unneeded by the receiver, and the reference sender clears input and output Unknown fields. Refute stage (codex, 2026-11-05 00:32): not refuted, severity medium, confidence high, conditional on the wallet attaching identifying metadata. Reproduced in memory with a mocked fetch.

## Quirk

- Headless workers started by the pipeline read ~/.claude/CLAUDE.md and write hand-off notes to ~/super/inbox/<branch>.md even though their output is already captured by the harness. Worth a prompt-level opt out in prompts/_common.md if the noise grows.

## Model pool (settled 2026-11-05 02:15 by the wT:p1 worker, commit e27c3cc)

The OpenCode Zen free tier entries (opencode:opencode/...) were dropped from config.json: keyless, anonymised, terms allow logging, models rotate. The opencode backend uses the Token Factory tff/ family only (key in /etc/opencode/opencode.json); Claude and Codex stay as the non-blocking minority. This was the human's decision.

## Entry point and cron (2026-11-05, worker wT:p1, master at dd270ad)

- The entry point is bin/run.sh and `bin/audit run` (renamed from nightly in 9a3dafb, d3ef3e3). Crontab: `17 02 * * * /home/agent/repos/payjoin-audit/bin/run.sh >>~/.cache/payjoin-audit/cron.log 2>&1`.
- The first cron run (2026-11-05 02:17 UTC) failed every model call because cron's PATH lacks ~/.local/bin, so no backend was found; 79 tasks had no usable answer, every model got disabled, and an empty run was committed as 73d8902 and reverted in 5f7f4fe. Fixes: the wrapper exports the user bin directories (5ce18b8) and the CLI refuses to start without a backend on PATH (dd270ad).
- Next scheduled run: 2026-11-06 02:17 UTC. Check ~/.cache/payjoin-audit/cron.log after it; a healthy run shows nonzero answer counts per model and a commit on master.

## Throughput (learned 2026-11-05 during the first full run)

- No per task rate limit in the pipeline. config.json concurrency: total 8 calls in flight, 2 per model. Timeouts: default 1500 s, audit and explore 2700 s, minority 900 s; idle limit 600 s (backends.IDLE_LIMIT). A 429 or quota error counts as a failure; four in a row disables the model for the run.
- Discovery is six agentic search tasks, all launched at once, so its wall clock is the slowest single call plus timeout-then-retry chains. In the first full run three of six first attempts (kimi-k3 twice, glm-5.3-uncensored once) ran the full 1500 s and were retried from scratch. Successful calls took 8 to 23 min and millions of tokens, nearly all cache reads.
- The github-code discovery task is bounded by GitHub code search quota: 10 requests per minute per token. Core quota (5000/h) is not a concern.
- The box (4 cores, 19 GB) sits near idle during a run; raising total to 16 and per_model to 3 or 4 for the audit stage is cheap in machine terms, the risk is Token Factory 429s disabling models.
- Observed by the human 2026-11-05 ~05:20 during the audit stage: only 2 or 3 headless agents run at once despite total 8. Cause in the code: the audit stage has prefer_uncensored, so its candidate list starts with the only two uncensored strong models (kimi-k3-abliterated, which is race, and glm-5.3-uncensored-fp8); per_model 2 caps each at two calls in flight, so the stage runs at most 2 to 3 tasks at a time and the total cap of 8 never binds. In the audit stage so far glm-5.3-uncensored-fp8 took 57 calls and kimi-k3-abliterated 43; every other model under 10. Fixes to weigh when the run is done: raise per_model (the real lever here), add more uncensored strong models, or let prefer_uncensored interleave censored strong models after the first slot.
- Biggest wall clock sink (measured 2026-11-05): scan.run_audit_tasks maps the 80 audit tasks in parallel, then calls process_finding (dedupe, then two refute seats, then a possible tiebreak) for every raw finding serially on the main thread. The first full run found 320 raw findings; serial processing ran at about 4.5 min per finding (51 done between 11:01 and 14:52), so that step alone is around 24 h. Fix to weigh: run process_finding through runner.map (the refute seats inside already use runner.map). With that, the step would take an hour or two.
- run.sh has no lock, so a hand run that overlaps the 02:17 cron would have two pipelines writing the registry and committing on master at once.

## Parallel pipeline (2026-11-05, worker wT:p1, branch parallel merged into master at bc00719)

- Findings are deduped and refuted concurrently, grouped by (project, class) with each group in order. Explore verifies proposed classes in parallel. Residual race: one defect reported under two classes of the same project can pass as new twice.
- Call labels for dedupe and refute carry a 6 character hash of the finding. Before this, a second finding of the same project and class reused the first one's logged dedupe verdict within a run (and refute, when the symbol matched). Only the killed 2026-11-05 run hit it (6 reuses); the 18 drafts from the limited run are unaffected.
- Provider pressure (429, rate limit, overloaded, quota, 502/503/504, ECONNRESET) is retried on the same model after 20, 40, 80 s and does not count toward disabling; every 3 such errors lower that model's slots by one. Config block retry.
- concurrency total 24, per_model 6 (a guess; trytokenfactory.dev documents no limit and the gateway sends no x-ratelimit headers). Candidates are reordered by free capacity before each ask, so uncensored preference now holds only while those models have a free slot. Tune from the model usage table after a run.
- Discovery races every call (runner.ask race=True) with timeouts_s.discover 900.
- bin/run.sh takes flock -n on ~/.cache/payjoin-audit/run.lock; a second instance exits 1, so cron overlapping a hand run is safe.
- README still names the removed opencode free tier as part of the primary pool; one stale line.
- Model table across the first two runs (ok calls, median seconds): glm-5.3-uncensored-fp8 112 at 238 s (p90 1019 s), nemotron-3-ultra 108 at 68 s (p90 151 s, no failures at all), kimi-k3-abliterated 74 at 240 s (83 cancelled in races, 4 timeouts), glm-5.3 43 at 90 s, kimi-k3 32 at 194 s (5 timeouts). The kimi race was a coin flip against a partner of the same speed, so ada2419 dropped its race flag; discovery still races. Open question for the human: prefer_uncensored on audit and refute makes the pipeline pick the two slowest strong models over nemotron-3-ultra, which is 3.5 times faster and never failed; dropping the preference, or adding nemotron-3-ultra to the preferred set, would be the next big wall clock win, at the cost of refusals being more likely on security prompts.

## Refusal fallback (2026-11-05, worker wT:p1, branch refusal-fallback merged into master at 526e139)

- Stages audit, refute and explore now set "uncensored": "fallback": censored primary models first, ordered by speed_s in the pool (measured medians: nemotron-3-ultra 67, glm-5.3 89, kimi-k3 210; uncensored glm 235, kimi-abliterated 240), uncensored ones only after a refusal or when the censored band is exhausted. "prefer" restores the old order, one line per stage.
- Refusal = REFUSAL regex in audit/runner.py (first person declines, policy wording, the codex "flagged for possible cybersecurity risk" error, "help with harmful content"), checked on raw text, backend errors and the string fields of parsed JSON. Logged as "refused", refused: true in the call record and model-stats.json, never disables a model, never reused from the cache. Watch for false positives: a model writing "I was unable to complete ..." in a notes field would be treated as a refusal and the answer discarded. The summary's Refusals section lists every one, so a run's data shows it.
- After a refusal the fallback is an uncensored model of the same tier; refute seats widen to the pool minus the finder's family if their list has none.
- run_audit_tasks processes each task's findings as soon as it answers (runner.map on_result, a processing pool of total/2 workers). Explore still waits.
- Pre-existing problems the worker saw: bin/audit check reports 6 registry problems created by this run's triage (btcpayserver-btcpayserver and btcpayserver-btcpayserver-bip78 duplicate btcpayserver and btcpayserver-bip78 by repository; depends_on names bip78-sender and payjoin-ffi resolve to nothing). Triage adds projects that the repo-sharing check then flags; the registry has grown to 471 projects. pytest leaves reports/runs/t and reports/runs/test behind, which run.sh would sweep into a run commit. Both are follow-ups.

## Registry fixes (2026-11-05, worker wT:p1, branch registry-fixes code commits merged into master at bc1935b)

- Root cause of the duplicate projects: repository comparison lower cased only the host, so BTCPayServer/... and btcpayserver/... looked different. repo_key() lower cases the whole path; project_by_repo also matches github.full_name and aliases. A rediscovered repository folds its sources and evidence into the known project (discover.fold_sighting).
- `python3 -I bin/audit check --fix` merges projects sharing a repository into the entry added first (then the one with scan history, then lower id), repoints findings, fingerprints and dependencies, and resolves depends_on names through the aliases (payjoin-ffi -> rust-payjoin) or parks them in depends_on_unresolved (bip78-sender is a role name, not a project). Idempotent. TO RUN against the main checkout once the current run has committed, then commit the registry.
- Triage now resolves dependency names as it writes them; maturity filters depended_on_by to known ids.
- tests/conftest.py redirects REPORTS to a temp dir, so pytest leaves nothing under reports/runs.
- The worker's data commit (5b255d5, the same fix applied to a registry snapshot) was dropped to avoid conflicting with the run.
- Worker's caveats: survivor choice is by added date (review the "merged X into Y" lines); nothing writes aliases except the merge; the triage prompt could be told to name repositories only.
