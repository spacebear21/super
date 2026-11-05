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
