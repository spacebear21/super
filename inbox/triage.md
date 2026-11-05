# triage: decision sheet ready

Branch `triage` in `~/repos/payjoin-audit-triage`, head 6f17781 (on top of cfec4d1, the triage report). Lint clean (35 tests, registry check 0 problems). No Claude subagents were used; the sheet was built from the registry JSON, the run's plan and rejected lists, grep, and the verifier outputs already in my scratchpad. No finding status, draft or registry file changed.

## What is there

`reports/triage/2026-11-05-decisions.md`, 247 lines, 80 questions, each with evidence in at most two lines and a `Recommended:` answer. Reply as "all defaults except Q4 keep, Q12 no".

| section | questions | content |
|---|---|---|
| A. Class verdicts | Q1 to Q23 | 3 classes to retire as noise, 6 to downgrade, 1 rule for the dependency class, 12 duplicate-class merges (keep one, retire the twins, move live findings first), then "all other classes keep" |
| B. Project verdicts | Q24 to Q45 | camlcoin to watch and what to do with its 55 drafts; the 7 zero-class projects one each; the BIP77 class list that gets the integration level (34 ids, gives Bull mobile 35 classes); clearing the integrations' areas allowlist; the 15 remaining needs_review projects one line each |
| C. Severity calibration | Q46 to Q66 | 9 rules (R1 to R9) and 12 reading-list findings with a proposed severity change |
| D. Finding verdicts | Q67 to Q79 | 8 false-positive questions covering 10 findings with status_reason text that names the pattern; 2 dissent cases; one fold-all duplicates list plus 2 pairs pulled out |
| E. Reading list | Q80 | the 19-item batch: 16 send now, 2 hold, one approve-the-batch question |
| F. Commands | table, 80 rows | one row per question: `python3 -I bin/audit set-status`, `retire-class`, or an `edit "..."` one-liner through a small helper defined at the top of the section that loads and saves via the registry module |

The helper and every edit shape in F were exercised on a copy of the live registry (re-class, re-raise from rejected.jsonl, levels and areas edits, severity edits); `check` on the edited copy reports 0 problems.

## Not settled

- `satoshiportal-payjoin` had no head in the 2026-11-05 plan and is not in `sync_failed`, although two of its areas match classes today; something in the planner or the clone kept it out. Worth a look before the next run, since it is the Bull Bitcoin exchange-side code.
- Q33 is my choice of which BIP77 classes an integration can violate; the human may trim or extend the list.
- Q75 (BTCPay sighash) needs a human check of which signers BTCPay feeds the proposal to; the sheet proposes recording it as medium and holding.
- `reports/triage/.2026-11-05.md.swp` sits untracked in the worktree (an editor has the report open); I left it alone.

When the answers come back I execute them in the order given in F (moves and duplicates before retirements), then run `check` and lint.
