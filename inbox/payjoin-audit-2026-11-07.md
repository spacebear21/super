# payjoin audit 2026-11-07

## 2026-11-07 02:35:39 UTC: watchdog: high failure rate

11 of the last 20 completed calls failed (notify above 50%, abort above 90%); 11 of 40 this run.

| model | calls | failed |
| --- | --- | --- |
| opencode:tff/deepseek-v4.1-flash-1017 | 7 | 0 |
| opencode:tff/deepseek-v4.1-flash-1017-uncensored-fp8 | 2 | 1 |
| opencode:tff/glm-5.3 | 11 | 4 |
| opencode:tff/glm-5.3-uncensored-fp8 | 1 | 1 |
| opencode:tff/kimi-k3.1-pro | 4 | 3 |
| opencode:tff/nemotron-3-ultra | 15 | 2 |

## 2026-11-07 04:16:59 UTC: fallback to codex:default

refute rust-payjoin-090f69-bip78-receiver-prepare-psbt-cl-PsbtContext::prepare-seat0: codex:default answered after opencode:tff/glm-5.3-uncensored-fp8 failed.

