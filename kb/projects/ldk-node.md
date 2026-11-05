# ldk-node (research for rust-payjoin's monitor and settlement design)

Three OpenCode research sessions studied LDK and ldk-node to inform how a payjoin session should classify what spent its contested prevouts and when to close. Their reports are under ~/review-drafts/ldk-research/. They are recommendations, not decisions anyone took yet.

## What the research argued, and why

- A watched outpoint should only be a hint to the chain backend about what to report; the session's own classification stays the single authority, as LDK's monitor re-derives meaning from the transaction rather than trusting the watch. Contested prevouts go to the receiver's own chain watcher, never to the sender or a public index.
- Persist candidates as provisional with height and block hash; persist a final outcome only after the caller's depth threshold. LDK can announce a close early because its monitor lives on to reverse it; payjoin has no monitor, so the depth before close stays a caller policy.
- Keep an explicit "unrecognised" class rather than LDK's close by elimination; do not conflate an unknown spender with a close. Do not adopt LDK's full confirmation ordering contract.
- A resolution token needs a session generation or the classifying state, or an old clone can classify and then close a newer session. If an audit trail is wanted, it is wallet owned and clearable on reorg, never in the append only log.
- Wallets own labels; payjoin gets its own label rather than reusing the wallet's transaction type. Classification must stay pure, derivable after close, and reachable without scanning every session (a session key or a cheap "spends contested prevout" predicate).
- Untested or unseen: a sender double spend that never pays the wallet may be invisible; deep reorg replacement after a payment has graduated; classification after cancel or close is an open gap.
