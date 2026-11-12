# mini-swe-agent evidence and harness selection — 2026-11-07

## Operator direction

"We use what works best, no need to marry our design."

mini-swe-agent's successful local run is a reason to reconsider the custom coding harness and Resonant's reuse assumptions. Ownership, sunk effort, and architectural preference must not outweigh verified delivery. Adopt or adapt the better working approach, and revise the design where evidence warrants it.

## Measured result

Source: `swebench-lab` commit `48dfa03`, report `reports/ekto-hello-world.md`; saved trajectories and independent judge output under `runs/ekto-hello/` in that repository.

- mini-swe-agent 2.4.6, local environment, one fresh directory per task, sequential runs, local llama.cpp router.
- Model: `qwen3-coder-30b-ekto-q4`, Q4_K_M, CPU only, six threads, context 32768. Request cap 4096 tokens, request timeout 900 seconds, task step limit 20.
- **7/7 passed:** Python, TypeScript and JavaScript through bun, Elixir, Rust, Bash, and C++.
- Independent judging required the file to exist, compilation where applicable, exit status 0, and exactly `Hello, world!` followed by a newline.
- Rust and C++ compiled with zero reported compiler errors or warnings under default flags. Other rows had no separate compile step; bun execution does not establish TypeScript type correctness.
- **1192 seconds (19.9 minutes), 55 model calls**, 79067 prompt tokens including cache hits, 3603 completion tokens.
- Mean of per-task rates: **19.65 prefill tokens/s, 5.50 generation tokens/s**. Pooled rates: 18.39 and 5.23 tokens/s respectively; prefill excludes cached tokens.
- The Vulkan embedder was stopped during the run and restored afterwards. Codex confirmed `ranger-embed.service` active/running and port 8091 `/health` returning `{"status":"ok"}`.

This establishes that this model and harness can complete these seven simple coding tasks locally with independently checked output. It also exposes substantial overhead: hello world took six to eleven model calls per language. A passing run alone does not establish acceptable throughput for repository work.

## Comparison with our pipeline

The [overnight dispatch note](2026-11-07-overnight-dispatch-lessons.md) records zero of five cards delivered autonomously, fifteen dispatches, twenty harness fixes, and frontier completion of every card. That is evidence against treating the existing pipeline as a proven default.

The two runs are **not a controlled harness comparison**: EKTO is a 30.5B model; the overnight Engineer was a 4B fine-tune, and repository changes were harder than hello world in empty directories. The EKTO result establishes neither a SWE-bench Verified score nor success on multi-file repository changes. It does not prove that changing only the harness would have rescued those five cards.

The practical conclusion is still consequential: mini-swe-agent is a working candidate to reuse, and the custom loop must justify further investment against it. Do not explain away the successful alternative to protect our design, or attribute the custom pipeline's failures to model capacity without separating the variables.

## Consequence for Resonant

1. Include mini-swe-agent in local Engineer adapter discovery before building or extending a custom tool loop. Keep only the surrounding orchestration and verification that has a demonstrated purpose.
2. When coding experiments resume, compare harnesses with the same model, quantization, chat template, sampling, resource settings, baseline revisions, tasks, and independent checks. Give each harness its native execution protocol and record any required adaptation and preparation cost.
3. Measure independently verified repository changes, regressions, end-to-end time, model calls/tokens, resource use, and frontier intervention. Report incomplete or timed-out work as such; compare whole-task delivery rather than generation speed alone.
4. If mini-swe-agent delivers better results and meets the required boundaries, adapt it and retire redundant custom machinery. If the design obstructs the better approach, revise the design and record the tradeoff. Preserve independent verification and authorized work state.

This note changes the design's selection policy; it does not deploy an adapter, select a new model assignment, or resume paused Sojourney/Trailblazer coding. Repository-scale evaluation remains pending. Docker image pulls, Hugging Face downloads, and other large transfers remain deferred while the metered-hotspot restriction is in force.
