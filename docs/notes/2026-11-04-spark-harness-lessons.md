# Spark edit-harness lessons — 2026-11-04

This is measured input to Resonant's design, not authorization to implement it or change the assigned models. The operator calls the desired experience FIM-like: a small, focused code edit should not require a large agent conversation. This trial uses native JSON range edits, not a model's native fill-in-middle tokens/objective. Compatibility with a true FIM protocol needs separate qualification.

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

## Lessons to carry forward

Give explicit small edits one canonical contract and a bounded original-source window before broad retrieval. De-duplicate by provenance; distinguish hard requirements from operational history. Retrieve/rerank relevant evidence within a task-scaled budget rather than filling a fixed allowance; expose each section's token cost and relevance. Make the editing format match the model/runtime and expose only permitted actions. Measure prefill, generation, compilation, acceptance tests and independent review separately. Preserve incomplete work; commit compiling checkpoints even when tests fail and file linked failure reports. Arrange sender-labelled, work-linked caller callbacks at launch; status files/timers are backup, not the handoff.

The approximately one-minute prefill target is an operator goal on this box, not a universal guarantee. A FIM-like baseline should be qualified with actual code/check outcomes before broad autonomy. No new subsystem or model substitution is decided by this note.

## Evidence and primary sources

Local evidence: `~/.local/state/trailblazer-dispatch/harness-comparison/` (messages, progress, partials, finalfocused result/release/checkpoint) and `model-research-reference/` (findings, reference.patch, RED/GREEN logs). Source base `ae973448`; deployed tool `8749605`. Issues Trailblazer:e2e2ab5/9ae2ace and VORTEX:fdf15bf/c581381.

[Model card](https://huggingface.co/XHToken/Spark-X2.6-4B), [DSpark instructions](https://huggingface.co/XHToken/Spark-X2.6-4B-DSpark-GGUF/blob/main/README.md), [pinned budget implementation](https://github.com/XHToken/llama.cpp/blob/22c0ae1871b60123c5afc008a78fe9c6fffa2858/tools/server/server-common.cpp#L1514), [template](https://huggingface.co/XHToken/Spark-X2.6-4B/blob/main/chat_template.jinja).
