# Memory: dreaming pass, transcript index, topic pages

## What it is
From your reading of Anthropic's post on the components of agent memory (2026-11-06): a nightly dreaming pass checks my notes against ground truth and proposes corrections on a branch, which I review and merge as soon as it ends. Done by Fable, the model that does the thinking; bulk reading may be delegated to Token Factory but only over public material. A transcript index makes every session on the box searchable. Project pages become index pages linking to topic pages with sourced claims.

## Where it stands
- Built and on the hub's main: `transcripts-index` (3,055 sessions, 49 s cold, 3 s warm, qmd collection `transcripts`), `super-dream`, schedule entries at 05:30 UTC (dream) and 04:45 UTC (interop matrix). Until the deploy, both scripts run from symlinks in `~/.local/bin`.
- The trial pass (40 turns, $6.48, ten minutes) made four commits; an Opus review found one real loss (a dropped holds list) and two wrong "unconfirmed" marks; merged with fixes.
- The first topic page exists (`kb/topics/witness-utxo-fee-issue.md`); the pass continues the reshape.

## What is asked
A number for the nightly turn cap. 150 is about $25 a night at the trial's rate; 40 was $6.48 and ran out before the pass could review its own commits. I would start at 80.

## Pointers
Instructions the pass follows: `dreaming.md` in this repo. Harness runbook, Dreaming subsection.
