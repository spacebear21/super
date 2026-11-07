# interop: matrix finished (steps 1 to 7)

Branch `interop` in `~/repos/payjoin-audit-interop`, head `1cd6a98c7323bc587aee698617d4b10da83aa772`, working tree clean. Nothing is pushed (push is read only by design).

## What is done

Eight commits on top of `e5189b2`, one per step plus the nightly results commit that step 5's done criterion asks for:

- `3445e49` Add the payjoin-cli version-skew rows
- `dfc9f24` Add bdk-cli as a participant
- `213b3bd` Add bitcoinjs payjoin-client as a participant
- `31c6a33` Record interop runs and render the page
- `645c08f` Add the nightly interop run
- `76a79f9` Record interop run 2026-11-07 (made by `bin/run-interop.sh --scenarios A01,B01` run by hand)
- `7aee5a9` Lint the interop tree with the audit's conventions
- `1cd6a98` Document the interop matrix

The earlier agent's four uncommitted files were mostly right and went into step 1. Its two real changes held up against the payjoin-cli sources: the 0.23 and 0.25 BIP 78 senders do print the original, so A16 and A18 stay runnable for them (the brief said to declare them), and "Sent fallback transaction txid" means an offered original, not a broadcast one.

Checks: `contrib/lint.sh --check` passes from a fresh clone of the branch (3 min 43 s cold, including the Core download), `cargo test` 24 unit tests, `pytest` 51, `bin/audit check` 0 problems. No bitcoind, payjoin-cli, bdk-cli or front.py processes are left over.

How long things take: a full wave 1 star (6 participants, 44 cells, 154 scenario runs) took 21 minutes with the debug build. The A01 and B01 star through `bin/run-interop.sh` took about 2 minutes plus the release build.

## What is not done, or differs from the brief

1. bitcoinjs payjoin-client does not pass A01 (step 3's done criterion). I kept the participant because it works. The library rejects every proposal from every rust-payjoin receiver with "The receiver added non_witness_utxo or witness_utxo to one of our inputs" and broadcasts the original. BIP 78 says the proposal MUST only fill the UTXO fields for the receiver's additional inputs, and rust-payjoin keeps them on the sender's inputs on purpose (its test `test_prepare_psbt_preserves_input_fields` asserts it). This looks like a real finding for the audit against rust-payjoin receivers. It needs a human to decide whether to raise it.
2. A18 fails for the 0.23 and 0.25 senders because of the verdict, not the participants. The receiver took exactly the `maxadditionalfeecontribution` the sender authorised (340 sat, 68 vB at 5 sat/vB), and BIP 78 bounds it by `originalPSBTFeeRate * vsize(sender_input_type)`. The verdict bounds it by the measured added vsize (67 vB) plus 1 sat. This is a harness decision outside the brief's steps, so I did not change `verdict.rs`. Suggestion: allow the input type's estimated vsize (68 vB for P2WPKH), or the authorised maximum.
3. bdk-cli's receiver needed another route for TLS, as the brief anticipated. `SSL_CERT_FILE` works for bdk-cli's own client. payjoin 0.25's `fetch_ohttp_keys` uses its own reqwest 0.12 client with only the built-in web PKI roots, and it opens no certificate file at all (checked with strace). `interop/participants/bdk-cli/front.py` is a plain HTTP front, used as the receiver's directory and relay. It forwards to the real HTTPS directory and to a real relay, runs detached so an offline receiver still has a mailbox, and exits when the environment's directory goes away. So bdk-cli receiver URIs say `pj=HTTP://LOCALHOST:<port>/...`.
4. For the step 5 hand run I set `PAYJOIN_AUDIT_INBOX` to a scratch directory and unset `HERDR_ENV`, so a test note would not wake you or pop a desktop notification. The note was written correctly there. The 2026-11-07 results in `76a79f9` were made from a dirty tree (steps 6 and 7 were not yet committed), so the page says harness dirty.
5. The schedule slot for `bin/run-interop.sh` (06:00 UTC suggested) still has to go into the harness repository's `schedule.toml`, which is outside this task.
6. I rewrote local history with fixup commits to keep one commit per step (nothing was pushed).

## The matrix, per participant pair (full star of 2026-11-06)

Fails that every cell inherits from the reference: A08 and A09 (payjoin-cli receiver panics with a taproot wallet), and B09 whenever payjoin-cli 0.25 or later is the sender (panic with a nested SegWit wallet).

- rust-payjoin (1.0.0) with itself: as in the brief. A08, A09 and B09 fail, A16 and A18 are declared, the rest pass.
- rust-payjoin-1.0.0 (cli 1.0.0-rc.2) in both roles: identical to the reference.
- rust-payjoin-0.25 (cli 0.2.0) as sender: A01, A16, B01, B17, C01, C05 and C08 pass. A18 fails (verdict bound, see above). A25 fails because a BIP 78 send to an unreachable receiver prints no fallback. B18 fails because the sender keeps polling after the receiver's session expired. B09 fails (panic).
- rust-payjoin-0.25 as receiver: A01, A25, B01, B17, B19 and all C scenarios pass. B18 is declared (no expiry option). A08, A09 and B09 are inherited fails.
- rust-payjoin-0.23 (cli 0.0.9-alpha) as sender: A01, A16, B01, B09 and the C scenarios pass, so the nested SegWit panic came in after 0.23. B17 and B18 fail on a real skew. payjoin 0.23 splits the URI fragment on `+` and newer receivers write `-`, so a 0.23 sender does not see the BIP 77 parameters and falls back to BIP 78 through the directory, which needs the receiver online. A25 fails as for 0.25, and A18 on the verdict bound.
- rust-payjoin-0.23 as receiver: B01, B17, C01 and C05 pass. BIP 78 receiver is declared (it makes its own certificate), and so are B18 and B19 (it forgets the session once the proposal is posted). B09 is inherited.
- bdk-cli 4.0.0 as sender: A01, B01, B09, B17, C08 pass. A25 fails (no fallback printed), and B18 fails as for payjoin-cli 0.2.0, which links the same payjoin 0.25. A16, A18, B19, C01 and C05 are declared (C01 and C05 because bdk-cli always speaks BIP 77 when the URI offers it).
- bdk-cli as receiver: B01, B17, C01, C05 pass. B19 fails because the receiver does not notice the original settling and times out after 15 s. BIP 78 receiver and B18 are declared. B09 is inherited.
- bitcoinjs payjoin-client (sender only, BIP 78): A25 and C05 pass (clean fallback). A01, A16, A18 and C01 fail on the UTXO field rejection above. A08 and A09 are inherited.

## Things I learned that the brief did not say

- corepc-node reads `BITCOIND_DOWNLOAD_DIR` at run time too, not only in its build script. With the variable set only for the build, every release cell was an infra error. The script now exports it for the whole run.
- The runner's tracing wrote to stdout, not stderr as the contract says, so the summary captured by the script picked up a log line. It now writes to stderr.
- bitcoinjs payjoin-client v1.0.1 has no lock file, so `npm ci` cannot work. The build uses `npm install --no-package-lock` and resolves bitcoinjs-lib 5.2.3.
- payjoin-cli's printed proposal txid is a prediction made before the sender signs, and it is wrong for a nested SegWit sender (the scriptSig changes the txid). The reference adapter now reports only txids the wallet has seen. That changed B09 for the 0.23 sender from fail to pass.
- payjoin-cli's and bdk-cli's receivers sometimes see the payjoin within their short mempool watch and close the session before the flow stops them, so the later resume finds nothing. Both adapters now remember a settlement and report it again. This was a hidden flake in B01, B09 and C01.
- A mailroom relay answers a proxied plain HTTP request for `/.well-known/ohttp-gateway` itself, with its own keys rather than the directory's. That is why a simple plain HTTP directory front was not enough for bdk-cli and front.py also stands in as the relay.
- The registry's bitcoinjs entry says roles sender and BIPs 78, so every other cell is unsupported automatically, as the brief said.
