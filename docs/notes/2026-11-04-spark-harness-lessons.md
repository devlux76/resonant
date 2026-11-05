# Spark edit-harness lessons — 2026-11-04

This is measured input to Resonant's design, not authorization to implement it or change the assigned models. The operator calls the desired experience FIM-like: a small, focused code edit should not require a large agent conversation. This trial uses native JSON range edits, not a model's native fill-in-middle tokens/objective. Compatibility with a true FIM protocol needs separate qualification.

## Model and quantization provenance

All trials here used **XHToken/Spark-X2.6-4B-GGUF, target Q8_0**, served as `spark-x2.6-4b:q8-dspark`, with the DSpark **BF16 draft** (precision per vendor card), greedy speculative sampling and n_max7. The target file SHA-256 `145c2aa606820663f26ad530b8fc141ed885e2e9ca29f9be7c3bfe8931d58b81` was verified against the vendor; a separate local draft hash was not recorded. Runtime: XHToken/llama.cpp `22c0ae1871b60123c5afc008a78fe9c6fffa2858`, CPU6threads, context131072. Per-trial `*-configuration.json` companions and `experiment-configuration.json` preserve exact settings beside the original result files without rewriting those results.

**Scope of conclusions:** these are results for this model, quantization, runtime and harness—not a claim about all code models or FIM-trained models. Another model or quantization could behave differently; no cross-model or cross-quant accuracy comparison was performed.

## Task and comparison oracle

The task is a31-line TypeScript contact module. A suffix-only `/p2p/<peer>` check accepts malformed addresses. The required change uses the existing multiaddr parser, validates its bytes and final peer component, and preserves limits, strings, protobuf handling and browser compatibility. The reference patch adds one import, a small validation block and two negative tests. In an isolated checkout the new tests fail on original code; the reference typechecks and passes88focused tests. It is reference-only and was not fed to Spark or merged.

## Confirmed findings

- Runtime: Spark-X2.6-4B Q8+DSpark, pinned XHToken22c0ae18 CPU fork, temperature1/top_p.95/top_k0, high thinking, reasoning budget2048, total output16384. The vendor implementation supports the reasoning-budget key. No quantization-accuracy comparison was performed; Q8 corruption is not established.
- The normal source-only job received a26KBpacket for31source lines: Request3943characters, repeated Design1987, Trail16010, plus an unrelated example1624. It spent408seconds to emit a24-token file-read request, using45percent of its generation window before editing.
- This path calls Trailblazer's Trail.build directly, not Scout's reranker. The default trail budget is4000tokens; broad seed nodes bypass the similarity floor, and no-vector fallback admits neighbours. The loop also adds a test example merely because the request mentions tests. Trace relevance and packet assembly rather than blaming a component not in this path.
- The earlier host said reading was over but still advertised read/search tools. It now advertises only the edit tool at submission. A changed-harness probe submitted edits in460.9seconds; the proposed code nevertheless had wrong ranges/import/API calls and failed compilation. A permissive parser cannot safely invent the missing correct code.
- A compact3091token prompt still timed out without edits; a full9081token prompt used468.9seconds for prefill and returned forbidden searches. One small sample does not prove general model capability or incapability, nor reproduce the vendor benchmarks.

## Final controlled trial

Only this diagnostic packet was compacted; the production retrieval path was not globally rewritten. The original source and digest appear once, with one concise task/API description and only the edit tool. Duplicate plan, operational history, unrelated retrieval, example and file-request round are absent. Model/samplers/budgets are unchanged. The reference implementation was not supplied. There is one15minute generation call, then bounded verification, followed by parking until Claude is available.

Measured input:991tokens, cache0, prefill44.865seconds (first-token latency and code correctness are separate metrics). This meets the operator's approximately one-minute prefill target; it does not yet establish a successful edit.

<!-- FINAL_OUTCOME_BEGIN -->
Final outcome: **compilation failed; no integration.** The cold input was991tokens; prefill took44.865seconds and first generated token arrived at48.589seconds. The model returned one valid native edit call in800.162seconds (13minutes20seconds), using4145completion tokens including reasoning/tool output. This meets the prefill goal but not the working-code goal.

The proposed replacement removed the function's closing brace (`TS1005: '}' expected` at contact.ts:42). It also treated the peer-ID string as a network address via `multiaddr(peer)` and compared component objects instead of the required final p2p peer value. These mistakes exist in the model's saved edit arguments before tool application. The compiler stopped verification; the full tests and two negative acceptance assertions did not run. No compiling checkpoint was falsely claimed.

Preserved failed candidate tree: `59c71f5b404b09e10d49a84075892ffee0bd7b51`; worktree `harness-fdf-20261104-finalfocused`. Automatic Git-bug report: `vortex:28e1358`. The full native reply, input, metrics, compile output and notification receipt are in `harness-comparison/finalfocused-*.json`. The direct callback was host-admitted (`delivery=started` with a turn ID).

Native supervision exited0 and resource release was confirmed. Fresh runtime verification found no open cases, active worker, queued task, resource lease or dispatch profile. OpenCode/tick are stopped; progress automation remains paused. The operator ordered parking until Claude is available; there is no retry, new admission, deployment or integration authorized by this result.

Interpretation: excess prompt data was a real, removable prefill cost. Removing it did not make this sample's generated code correct. One final request does not identify every remaining cause or prove that the model is generally incapable. The requested2048reasoning budget is recognized by the vendor implementation, but separate effective reasoning-token accounting was not measured here;4145total completion tokens are not themselves proof that the thinking cap failed. Further format/model comparisons and production retrieval changes remain parked, with the independently verified reference patch available as an oracle.
<!-- FINAL_OUTCOME_END -->

## Additional controlled comparison: restore the detailed instructions

The operator authorized one additional trial after the initial park. Only the task-description paragraph changed: the compressed510-character instruction was replaced by the original1836-character detailed source-only plan verbatim. The system prompt, original source and digest, edit schema, model/quantization, sampler/budgets and acceptance checks stayed unchanged. No prior model output or reference implementation was included.

| Measurement | Compact description | Detailed original plan |
|---|---:|---:|
| Model / target quant | Spark-X2.6-4B / Q8_0 | Spark-X2.6-4B / Q8_0 |
| Draft / runtime | DSpark BF16 / XHToken22c0ae18 CPU | DSpark BF16 / XHToken22c0ae18 CPU |
| Input tokens, cold cache | 991 | 1,317 |
| Prefill | 44.865 s | 104.967 s |
| First generated token | 48.589 s | 108.670 s |
| Total elapsed | 800.162 s | 900.053 s, hard timeout |
| Complete output tokens | 4,145 | Unavailable after cancellation |
| Delivered edit | Yes; compilation failed | No complete submission |
| Compilation / acceptance tests | Compile failed; tests not run | Neither started |

The detailed run preserved partial reasoning that discussed the correct parser/property/final-component approach, but produced no executable patch. That is not successful code generation or evidence that the task is solved. Restoring the detailed plan did not improve task completion within this single15-minute trial. These samples do not establish a general causal effect of prompt length or verbosity. Although model/settings were fixed, machine load and scheduling were not controlled; the observed per-token prefill rates also differed. No output-token total should be inferred from the cancelled stream's zero-valued usage placeholder.

Evidence: `detailedfocused-comparison.json`, `detailedfocused-configuration.json`, the saved input/partial/error/result/release/notification artifacts and `progress.jsonl` in the harness-comparison directory. The native job exited0 and release returned`:ok`; fresh verification found no open cases, active worker, queued task, resource lease or profile. The workload and alarms are parked again. Further diagnosis, including effective thinking-budget enforcement, remains unproven and is deferred until coordination with Claude resumes.

## Continuous-generation time-limit experiment

The operator authorized one more continuous call with the same detailed-plan messages, source, edit schema and model settings (Spark-X2.6-4B Q8_0 target, DSpark BF16 draft, pinned XHToken22c0ae18 CPU runtime). Native observations were scheduled at20minutes and each5minutes afterward without restart/unload. The16384-output-token ceiling was unchanged; the normal pipeline's15-minute policy was not changed.

The model returned a tool call **before any extension was needed**: cold1317-token prefill65.817seconds, first token70.519seconds, complete response114.792seconds (1minute55seconds),188completion tokens. It only added the named import plus a duplicate comment; the validation body was unchanged. Typecheck and86existing tests passed. The first new malformed-address rejection assertion failed; the second assertion was not reached.

The compiling partial was preserved by a normal hooked checkpoint commit `574169e7bdf08e822607fb303bce0bbdd4360f63`, tree `5637c110f87e9fd32a2aee26af4a475671dbb9fc`; failed checks created `vortex:07b8e61`. Its graph index was refreshed; the checkpoint initially reported3129pending vectors, so this is not a claim that all asynchronous embeddings were complete. Nothing was integrated. This demonstrates why compilation and existing green tests do not establish the requested behavior.

**Test-setup defect/confound:** the restored plan says to read the file once, but the preloaded-source trial exposes only the edit tool. The model's61-byte reasoning trace explicitly intended a read call, then it emitted an edit submission. That is concrete evidence of instruction/tool mismatch; whether it fully explains this response is unproven. The same ambiguous read instruction existed in the prior detailed-plan timeout. The harness interprets `submit_edits` as a complete batch, but this response does not establish that the model intended the task to be finished. Future qualification must align the available tools with the requested phase instead of treating this as clean proof of model failure.

There was no20/25/30-minute milestone because generation ended early. This run cannot determine whether the previously cancelled generation would have completed with30minutes; it is a separate stochastic trajectory. Extra allowed time did not deliver the task in this sample. Evidence is `continuousfocused-comparison.json`, configuration/reply/checkpoint/check/release records and progress log. Native supervision exited0 and cleanup was verified clear. All further work remains parked for Claude/operator coordination.

## Audited, consistent-contract trial: one-hour cutoff

The operator declared the preceding read-instruction/sole-edit-tool trial invalid for the time-limit question and authorized a carefully audited replacement. This is a new corrected-contract trial, not an isolated replay of the invalid prompt. It used the same Spark-X2.6-4B Q8_0 target, DSpark BF16 draft and pinned runtime/samplers/budgets; the input and tool text now explicitly say the source is already supplied and one final submission must include both changes. Stale read, retry and15-minute model instructions were removed. Exact messages/schema/configuration and audit findings are frozen separately.

Before inference, the actual full checker was tested on both controls: the original code passed86existing tests and failed **both** independent negative probes; the reference passed88tests and **both** probes. A real saved native edit reproduced its exact committed file through the same decoder/application path, and a stale digest was rejected without any write. Source/base/numbering/hash and sole-tool schema were checked. The reference implementation was never included in model input. These controls validate the task/checker/codec contract; they do not qualify every black-box model/runtime behavior or make a single stochastic sample a general benchmark.

Outcome: **no complete edit submission in one hour**. The input was1,316cold tokens, prefill62.755seconds, first generated token66.619seconds. Generation continued in one context without restart or repeated prefill, but mostly repeated discussion of the API and import placement. At the cutoff,7,476bytes were saved in the reasoning field and27,308bytes in visible content; the visible content was also largely self-analysis. No complete tool call arrived, so compilation, acceptance tests and checkpoint publication never started. The cancelled stream's total output-token count is unavailable, not zero. The recognized2048reasoning-budget parameter is not proof that self-analysis in visible content is bounded; effective behavior remains a qualification item.

**Overall-budget enforcement gap:** the standalone diagnostic originally had a six-hour emergency watchdog rather than retaining the one-hour overall ceiling. The operator reasserted the hour limit. Its existing native backstop was then scoped to this controller and rescheduled to one hour from the original start, preserving the standard partial-output and cleanup path. The model error record reports3,600.050seconds; release returned`:ok`, supervision exited0, and fresh verification found no open cases, active worker, queue, resource lease or profile. The result was directly delivered to the caller. Future phase extensions must not implicitly waive an overall work-contract deadline; diagnostic and production paths need the same explicit budget authority.

Evidence: `audited-preflight.json`, `AUDIT.md`, both control records, `auditedcontinuous-comparison.json`, frozen input/tools/configuration, complete deltas/partial/termination/error/release/notification and hour-limit override records. The payload contains no prior model answer or reference patch. This trial establishes failure to deliver within the tested hour, not impossibility for other prompts/models/quants/runtimes or with unlimited time. All further work is parked; no code was integrated or pushed.

## Lessons to carry forward

Give explicit small edits one canonical contract and a bounded original-source window before broad retrieval. De-duplicate by provenance; distinguish hard requirements from operational history. Retrieve/rerank relevant evidence within a task-scaled budget rather than filling a fixed allowance; expose each section's token cost and relevance. Make the editing format match the model/runtime and expose only permitted actions. Measure prefill, generation, compilation, acceptance tests and independent review separately. Preserve incomplete work; commit compiling checkpoints even when tests fail and file linked failure reports. Arrange sender-labelled, work-linked caller callbacks at launch; status files/timers are backup, not the handoff.

The approximately one-minute prefill target is an operator goal on this box, not a universal guarantee. A FIM-like baseline should be qualified with actual code/check outcomes before broad autonomy. No new subsystem or model substitution is decided by this note.

## Evidence and primary sources

Local evidence: `~/.local/state/trailblazer-dispatch/harness-comparison/` (messages, progress, partials, finalfocused result/release/checkpoint) and `model-research-reference/` (findings, reference.patch, RED/GREEN logs). Source base `ae973448`; deployed tool `8749605`. Issues Trailblazer:e2e2ab5/9ae2ace and VORTEX:fdf15bf/c581381.

[Model card](https://huggingface.co/XHToken/Spark-X2.6-4B), [DSpark instructions](https://huggingface.co/XHToken/Spark-X2.6-4B-DSpark-GGUF/blob/main/README.md), [pinned budget implementation](https://github.com/XHToken/llama.cpp/blob/22c0ae1871b60123c5afc008a78fe9c6fffa2858/tools/server/server-common.cpp#L1514), [template](https://huggingface.co/XHToken/Spark-X2.6-4B/blob/main/chat_template.jinja).

## Repairs queued for Claude

The operator classified the cache/restart findings as spec or implementation bugs and assigned follow-up to Claude when available. No repair has started:

- **trailblazer:95b9b73**: qualify and repair production prompt-cache reuse across read/submission and retries. Normal second-call logs show zero cached tokens; the changed toolset can invalidate the prefix. Intentional cold-cache comparison runs are separately labelled and should not be mistaken for production policy. Preserve the sole-edit tool restriction and resource fencing.
- **trailblazer:6ff32b2**: specify and qualify interrupted-generation continuation versus fresh-context restart. Preserved partial reasoning is not currently resumed; outbound reasoning omission and effective thinking-budget accounting need investigation. Prefix caching alone does not continue generation. Do not replay incomplete tool calls, silently extend timeboxes, or assume optional reasoning preservation is universally appropriate.

Acceptance requires actual model/quant/runtime-specific cache and completion measurements, safe invalidation, preserved unfinished work and updated specs. Both cards remain queued/parked for Claude; no wake, model run or new subsystem is authorized by this note.

## Sampling and repetition settings checked after the hour run

The recorded Q8_0+DSpark child properties and request adapter show repeat_penalty1.0, repeat_last_n64, presence_penalty0 and frequency_penalty0. The request does not override the64-token server lookback. DRY multiplier0 also disables its sequence-repetition penalty (its recorded lookback is64). Thus these settings provide no repetition deterrent; increasing lookback alone cannot help while the corresponding penalties are disabled. This is configuration evidence, not proof that enabling a penalty would fix the observed loop.

Temperature1.0, top_p0.95, disabled top_k, repetition_penalty1, presence0 and frequency0 match the vendor card's examples. The card uses top_k0 in Transformers and-1 in SGLang; these represent disabled filtering in those APIs. It does not prescribe a repetition lookback. min_p0, typical_p1, XTC0 and Mirostat0 disable those additional samplers; no vendor-specific mandate for them was identified. Thinking is enabled/high. The requested2048thinking budget and16384total-output limit are harness choices, not exact vendor-benchmark budgets. The hour trial ended on wall clock, not a reported token limit.

Supplemental metadata is saved in `sampler-card-comparison.json` beside the frozen trial files; original configuration artifacts were not rewritten. Sources: [model card](https://huggingface.co/XHToken/Spark-X2.6-4B) and [pinned runtime parameter documentation](https://raw.githubusercontent.com/XHToken/llama.cpp/22c0ae1871b60123c5afc008a78fe9c6fffa2858/tools/server/README.md). No settings, services or paused workloads were changed by this check.
