# payjoin-audit

A scheduled multi model audit of Payjoin implementations, in ~/repos/payjoin-audit on master. Its README and CLAUDE.md say how it works and its standing rules; this file holds why it is the way it is.

## Decisions and their reasoning

- **Model pool is the Token Factory family only** (human, 2026-11-05). The OpenCode Zen free tier was dropped: keyless and anonymised, the terms allow logging, and the models rotate or vanish. Claude and Codex stay as minority seats so a login or permission problem never blocks a run.
- **Censored models first, uncensored as the fallback after a refusal** (human, 2026-11-05). The original design preferred uncensored models for finder and refuter seats to avoid refusals on security prompts. Measured over two runs, that pinned the audit and refute stages to the two slowest strong models (about four minutes a call) while a censored one answered in a minute and never failed. The human chose speed with data collection: refusals are counted in each run's summary, and the old preference is one config line per stage if the refusal rate turns out to matter. Watch for false positives of the refusal pattern, which is calibrated on wording and could discard an honest answer that says "I was unable to complete" something.
- **Race only where calls stall.** Racing kimi-k3-abliterated against a partner of the same speed was a coin flip and cost a wasted call every time; the human called it out and the flag was dropped. Discovery still races, because those agentic searches stall for the full timeout often enough that a partner buys real wall clock.
- **Maximum parallelism, numbers guessed** (human). Token Factory documents no rate limit and its gateway sends no rate limit headers, so 24 calls in flight and 6 per model are a guess to be tuned from the model table of a real run. Rate limit errors back off and shrink a model's slots instead of counting toward disabling it, so a burst cannot knock models out of the pool.
- **Stage barriers**: only the boundary between the audit tasks and finding processing was worth removing; discovery, triage and the plan feed each other. The human: "not a huge deal either way".
- **Resume over restart.** Logged answers under the same run name are reused, so killing and restarting a run after a code or config change is cheap. Start a resumed run with the old run name after midnight UTC, or it starts from scratch.
- **Duplicate projects merge into the entry added first.** Survivor choice is by date added, which was right for the cases seen; review the merge lines a fix prints. Dependency names models invent that match no project (bip78-sender is a role, not a repository) are parked as unresolved rather than guessed. Telling the triage prompt to name repositories only was considered and left alone.
- **Nothing in the repo sends a report.** Drafts are reviewed and sent by the human. Unchanged since the start.

## Learned

- The first full run was on track for a day and a half: eighty audit tasks ran in parallel, then their 320 findings were deduped and refuted one at a time. Lesson for any pipeline: after a parallel map, look for the serial loop that consumes its output.
- Cron on this box starts with a bare PATH, so per user CLI installs are invisible to it. The first scheduled run failed every model call and committed an empty result before anyone noticed; the CLI now refuses to start without a backend on PATH.
- Call labels that do not identify the finding let the resume cache hand one finding's verdict to another finding of the same project and class. Found while parallelizing; only one killed run was affected.
- Repository identity must be compared case insensitively on the whole path, not just the host, or the same GitHub repository registers twice under different capitalisation.
- Discovery calls are agentic searches of 8 to 23 minutes and millions of tokens, nearly all cache reads; the GitHub code search quota (ten requests a minute) bounds the code search task, not the pipeline.
- A test suite that writes run directories into the repo's reports tree gets its output committed by the run script. Tests must write to a temporary directory.
- The pipeline's headless Claude workers inherit the global hand-off rule and write inbox notes named after the audited repository's branch. Those notes are noise.
- The Herdr status of the pane running the pipeline flips as it spawns agent CLIs; wake-ups about that pane are noise too.

## Process

- A run is bin/run.sh, from cron at 02:17 UTC or by hand; a lock keeps the two from overlapping. The log under ~/.cache/payjoin-audit/logs and the model table in the run summary are where to look when it seems slow.
- After any pipeline change, kill the run and restart it under the same run name rather than waiting; it reuses what was logged.
- 18 draft reports from the first limited run await the human's review.
- **Models are ranked by capability first, speed second** (human, 2026-11-05: use the most qualified open-weight models while they are free). Ranks come from the Artificial Analysis Intelligence Index, v5 where it exists and v4 figures otherwise; a one point gap is noise, so models within noise are tied and speed breaks the tie. Uncensored and abliterated variants share their base model's rank because nothing measures what the modification costs. Result: kimi-k3.1-pro is the strongest open-weight model reachable (served by the gateway though no catalog listed it), then deepseek-v4.1-flash, glm-5.3 and kimi-k3 tied, then the flash models; nemotron-3-ultra ranks near the bottom despite being fastest and most reliable, and nemotron-3.5-lightning last. muse-spark-open sits in the catalog but the gateway rejects every call, so it stays excluded until Token Factory serves it; its model card promises near total refusal of cyber prompts, so expect the fallback band to catch it if it is ever enabled.
- Lesson: the opencode provider catalog and what the gateway actually serves differ in both directions; check the gateway's model list, not the catalog, before adding a model.
- **A finding found twice at the same place after a project merge is folded into the earlier id, and the other record stays as a duplicate with a suffixed fingerprint rather than being deleted**, because its draft file may already have been read by a human. Open rule: the kept finding keeps its own status even if the other was the open one; not yet seen in the data.

## Learned in the 2026-11-05 triage of the drafts

- **The Token Factory verifier models never say "false-positive".** kimi-k3.1-pro (high and critical) and glm-5.3 (the rest) verified 175 drafts against the clones and did not reject one; the false positives and inflated groups in the triage file rest on reader reports and the worker's own checks. Treat those model verdicts as advisory confirmation, not as filtering. A verify prompt that forces a real rejection path is the fix to try before the next run leans on them.
- **Bull Bitcoin's own repositories were never scanned** (bull-bitcoin-mobile, satoshiportal-payjoin, bull-bitcoin-exchange have no last_scanned and no findings), so the Bull priority was served only through their dependencies. Scanning them is the obvious next run.
- **Scaffold repositories inflate the draft count.** camlcoin produced 55 drafts from a scaffold whose shipped receiver never contributes a UTXO; four issues cover it. Expect the same shape from other stub projects and read the project before reading its drafts.
- **A critical can be unreachable because of another finding** (bitmask: one finding makes every payjoin fail at signing, which masks the critical). Such pairs go out as one issue.

## Learned while sizing the next run (2026-11-05, super)

- **Integrations are nearly unscanned by construction.** A class applies to a project by its `levels`, and only 16 of 302 active classes list `integration`; the rest apply to implementations only. Bull Bitcoin mobile (integration, BIP77 only) therefore matches only the dependencies class, which its own `areas` allowlist excludes, so it has zero tasks on any run; Cake, satoshiportal-payjoin, bitmask, joininbox and the Flutter plugins get one to three. Seven of the 17 never-scanned active code projects have no applicable class at all (also fabric, payjoin-bitcoin-uri, samourai-extlibj, sideswap-rust, nobank, the BTCPay payjoin plugin), mostly empty `bips` or `roles`. Fix is in the registry (levels on the classes that integrations can violate, bips and roles on the projects), not in the pipeline.
- **The refute stage was nemotron-3-ultra in practice**: 358 of about 500 refuter seats on the drafted findings, and the triage worker found its verdicts restated the draft. Only 50 of 301 raw findings were refuted. The capability ordering already committed changes this from the next run; judge the refute stage again on that run's rejected.jsonl.
- **Any commit on a project's default branch rescans the whole project**, every area and class, not only the areas whose code paths changed; rust-payjoin alone is 12 tasks a night. Re-found findings are caught by the exact fingerprint (project, class, file, symbol) or by a fast-model dedupe against at most 40 of the project's existing findings, class first; camlcoin already has 55, so its window is short.
- **Explore's accepted classes become active at once and cost one single-class task per applicable project per area** on the next run (three classes from the last run produce 22 one-class tasks). Worth batching into the next full rescan of each project or raising the cap for them.
- **The plan's order** is never-scanned and changed projects first (never-scanned ahead, by empty last date), then unchanged projects with new classes, cut at 80 tasks; rechecks are open findings on changed projects in registry order, cut at 40 with no severity priority.
- **What a human verdict does downstream**: a terminal status (invalid, irrelevant, wontfix, duplicate, fixed) keeps the finding out of rechecks, makes its fingerprint a silent drop on re-discovery, and puts its status and reason text into every future audit prompt for that project as "do not re-raise". Retiring a class removes it from every scan and closes its open findings. Reasons that name the pattern generalize; "not a bug" does not.


## Human answers to the 2026-11-05 decision sheet (partial, 2026-11-06)

- camlcoin is a watch project, so none of its findings is worth reporting (Q9: park all). The six non-Payjoin projects are excluded (Q10). Class severities (Q3) and the needs_review confirmations (Q13) are decided case by case, not as a batch.
- **BTCPay sender sighash gap (Q35): record as a medium finding.** The human already reported it to BTCPay Server and to the BIP78 authors Nicolas Dorier and Kukks by email on 2025-08-20; no reply and no action known. So its status is reported, not drafted, and the recheck stage should watch it.
- Still waiting on the rest of the sheet; the reading list (Q37) and the command section were not understood and were re-explained in plain words.

## Where the 2026-11-05 run's hours went (measured from the call records)

- The 02:17 cron start died in 23 seconds (no opencode on PATH). The real run went 02:55 to 20:20 with four restarts (15:10, 15:52, 16:11 after config commits). The audit stage (04:10 to about 11:00) ran under the old config: 8 calls in flight, 2 per model, uncensored models preferred, so effectively four slots for 160 audit calls (80 tasks, 88 ok) of 7 to 8 minutes each. Refutes then ran one group at a time until the 15:10 restart raised concurrency to 24 and 6. Censored-first applied only from 16:11; the quality ordering and kimi-k3.1-pro landed at 18:39 and were never used in a run.
- Call time by stage: refute 29 hours (546 calls, median 90 s), audit 18.5 hours (median 288 s, mean 417 s, 2701 s cap hit), triage 7.5 hours, dedupe 5.3 hours, tiebreak 2.8 hours, discovery 2.6 hours (27 calls, 6 ok, racing). About 66 call-hours in all, so about 3 hours of wall clock at 24 in flight if nothing stalls. kimi-k3 refutes took 15 minutes each (906 s mean) against nemotron's 2 and glm-5.3's 3; a slow strong model at the head of the order holds its six slots and the spread rule moves the rest.
- Expectation for the first run under the new config (2026-11-06): 3 to 5 hours if kimi-k3.1-pro answers in minutes, longer if it behaves like kimi-k3. Measure it from this run's call records and set its speed_s.
