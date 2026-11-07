# bitcoinjs payjoin-client rejects every rust-payjoin proposal

## What it is
The interop matrix's first full run found that bitcoinjs payjoin-client (BIP 78 sender) rejects every proposal from every rust-payjoin receiver with "The receiver added non_witness_utxo or witness_utxo to one of our inputs" and broadcasts the original. BIP 78 says the receiver MUST only fill the UTXO fields for its own added inputs; rust-payjoin keeps them on the sender's inputs on purpose (its test test_prepare_psbt_preserves_input_fields asserts it, and the witness_utxo advisory work relies on those fields staying for hardware wallet senders).

## Where it stands
Reproducible on regtest: cells A01, A16, A18 and C01 with bitcoinjs as sender fail on this, and only this. The matrix results page shows it. Not filed as a finding yet.

## What is asked
Decide whether to raise it against rust-payjoin (the spec text is on bitcoinjs's side, the practical reasons are on rust-payjoin's: Trezor needs the sender's non_witness_utxo to sign and Ledger needs it to show the fee) or against bitcoinjs (strictness beyond what interop needs; the library is unmaintained since 2021). My recommendation: file it as a low interop finding against rust-payjoin with both sides stated, and let the BIP text be clarified rather than the behaviour changed.
