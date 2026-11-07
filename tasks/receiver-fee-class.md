# New class: receiver bounds the fee rate it accepts

## What it is
The review stage's live trial on Cake PR 3363 missed the regression where the receiver honours the sender's minfeerate up to 10,000 sat/vB. It was not a finder miss: no active audit class says a receiver must bound the effective fee rate it accepts from the sender's minfeerate, so the check was never in the prompt.

## What is asked
Yes or no to adding a class: area fees, role receiver, levels implementation and integration, with a BIP77 or BIP78 quote on receiver fee contribution. If yes, I have it added and the Cake finding filed under it.
