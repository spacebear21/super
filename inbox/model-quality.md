# model-quality: favour capable Token Factory models

Branch `model-quality`, worktree `~/repos/payjoin-audit-models`, off master bc1935b.
Head 39af33a. Two commits, one per item. The run in wT:p3 and the main checkout were not touched.

## What I did

1. **490c947 Add muse-spark-open and kimi-k3.1-pro to the pool.** Only muse-spark-open was missing from the pool compared with `opencode models tff`. But the gateway (gateway.trytokenfactory.dev, a LiteLLM front) rejects it: every call, through opencode and with curl, answers 400 "Invalid model name", and it is absent from the gateway's `/v1/models`. The opencode provider config at `/etc/opencode/opencode.json` lists it anyway, so the catalog there is ahead of what is served. The same `/v1/models` call shows a model nobody listed: `kimi-k3.1-pro`. It answers with curl, and through opencode once the provider config has a `"kimi-k3.1-pro": {}` entry (I proved that with a temporary copy of the config via `OPENCODE_CONFIG`; the system file is unchanged). Both now have pool entries with family, tier and censorship flag (muse family `meta`, both `strong`, neither uncensored) and both sit in `excluded`, so a run never burns failures on them. `excluded` is documented in the README.

2. **39af33a Order candidates by quality before speed.** Every pool entry has `quality`, a rank with 1 best. `Pool.candidates` sorts each tier group by (band, quality, speed_s) in every stage, including stages without an uncensored mode, which were random before. `_spread` is untouched, so load still moves to the next model when the preferred one has no free slot. Strong tier order for audit is now glm-5.3, kimi-k3, nemotron-3-ultra, then the uncensored band glm-5.3-uncensored, kimi-k3-abliterated. Fast tier for dedupe: deepseek-v4.1-flash, its uncensored twin, glm-5.3-flash, its twin, minimax, nemotron-3-super, nemotron-3.5-lightning. Tests updated and added.

## The ranking and why

I used the Artificial Analysis Intelligence Index v5.0.1 as the one scale that covers nearly every model, read today from each model's page, and fell back to the v4.x figures that Meta's and NVIDIA's own tables quote where v5 has no entry. Sources are in the commit message.

| rank | models | evidence |
|---|---|---|
| 1 | claude:opus | 59 |
| 2 | claude:sonnet, codex:default (GPT-6.1 Sol, the codex account default) | 55, 55 |
| 3 | kimi-k3.1-pro | 46, the best open-weight model by a wide margin |
| 4 | deepseek-v4.1-flash-1017, glm-5.3, kimi-k3 (and their uncensored variants) | 33, 32; kimi-k3 has no v5 page (retired) but tied glm-5.3 on v4.3 (44 vs 45) and beats it on Terminal-Bench 2.1 and DeepSWE in Meta's table, so it is tied |
| 5 | minimax-m3.1-flash, glm-5.3-flash | 29, 28 |
| 6 | muse-spark-open | 25; Meta claims 39 on v4.x, still below glm-5.3 and kimi-k3 |
| 7 | nemotron-3-ultra | 12 on the current leaderboard, 23 on v4.x in Meta's table, Terminal-Bench 2.1 53.9 against 84 to 85 for glm-5.3 and kimi-k3 |
| 8 | nemotron-3-super | 36 against the Ultra's 48 on v4.0 |
| 9 | nemotron-3.5-lightning | 6 |

The index versions do not mix well: v5 rebased everything down and rewards reasoning at max effort, so a 1 point gap is noise. I tied models within noise and let speed_s break the tie, which is what the task asked for. Uncensored and abliterated variants share their base model's rank because nothing measures what the modification costs.

## What I need

- **One line in `/etc/opencode/opencode.json`:** add `"kimi-k3.1-pro": {}` under `provider.tff.models`, in the provisioning source too since rootfs is wiped on rebuild. Then drop `opencode:tff/kimi-k3.1-pro` from `excluded` in `config.json`. It is the strongest open-weight model we can reach for free, and the gateway serves it today. I did not edit the system file while the run in wT:p3 is making opencode calls against it.
- **muse-spark-open** stays excluded until the gateway serves it. Worth asking Token Factory; the catalog lists it, the gateway does not.

## Not settled

- Lint: ruff, shfmt, shellcheck, codespell and the 35 tests pass. `bin/audit check` still reports the 6 known registry problems that master carries because the data commit from registry-fixes (5b255d5) was not merged while the run writes the registry. `check --fix` on this branch brings it to 0 problems; I did not commit that data again, same reasoning as before: run it on the main checkout once the run lands.
- `kimi-k3` is absent from the gateway's `/v1/models` yet every call to it succeeds, so I left it in. If it vanishes, kimi-k3.1-pro is the replacement.
- No speed_s for the two new models; the first run that uses them measures it. Kimi K3.1 Pro answered a trivial prompt in 34 s through opencode, Kimi K3 in 17 s.
- Muse Spark Open's model card reports 99.2% refusal of cyber jailbreaks, so expect refusals in audit and refute if it is ever enabled; the fallback band covers that.
- kimi-k3.1-pro output ended with a stray `<|close|>response` token in the raw gateway reply; `extract_json` tolerates trailing text, but watch the first run's call logs.
