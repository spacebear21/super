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

## Open question for the human (2026-11-05 02:11, from the wT:p1 worker)

The model pool in config.json includes nine opencode:opencode/... entries (OpenCode Zen free tier: keyless, anonymised, prompts may be logged or used for training, models rotate). The tff/ models (Token Factory, key in /etc/opencode/opencode.json) carried most of the load and cover every stage. All audited repos are public, so nothing sensitive leaks. The worker offers to drop the free tier entries in one commit if the human prefers. Awaiting the human's answer.
