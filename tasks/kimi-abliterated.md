# Drop kimi-k3-abliterated from the audit pool?

## What it is
The audit pool's uncensored fallback models take a finding when a censored model refuses. kimi-k3-abliterated is one of them.

## Where it stands
In the 2026-11-07 run (the first from cron) it was called 14 times and answered none: every call ended as a timeout or stall, eight of them at the 1500 second cap. Each stalled call held a refute seat for up to 25 minutes. The other uncensored models answered normally (glm-5.3-uncensored-fp8 309 calls, 4 failed; deepseek uncensored 44 calls, 1 failed). The Kimi models were already the slow ones in the 2026-11-06 probe.

## What is asked
Yes to dropping it from the pool, or keep it and say why. You made the last two pool drops yourself, so this one is yours too. If yes, I have a worker remove it from config.json and the model notes.
