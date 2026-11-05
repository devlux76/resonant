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

## Template follow-up: file/render checks pass, runtime integration not fully cleared

A read-only follow-up parsed the actual Q8_0 GGUF metadata without loading tensor data and fetched the vendor template/tokenizer/generation configuration at revision `aa074aa851209a683aeaf5f99a8e25a642d94541`. The GGUF embedded template and recorded runtime template are **byte-identical to the vendor tokenizer_config.json template** (SHA256 `93d6aa906ec72365efcab3c0109dba83148bdc9bec7c55848623d17483693eda`). The standalone vendor chat_template.jinja has different formatting/hash, but both produce identical4942-byte offline renders of the exact audited request and tool definition, with high-thinking instructions and the assistant `<think>` prefix. Earlier eight fixtures also matched and a recorded live apply-template probe correctly rendered tool/history argument escaping.

GGUF BOS0/EOS1/PAD2 and disabled automatic BOS/EOS insertion match the vendor configuration. Runtime capabilities report system roles, tools, tool calls and reasoning effort/preservation supported. There is no confirmed raw-template-file mismatch from these checks. The vocabulary also contains FIM markers, but their presence alone does not qualify a native FIM protocol or its quality.

Limits: this was offline Jinja2 rendering plus previously captured runtime evidence, not a new live minja/grammar trace of the hour-long request. Full HF-versus-GGUF tokenization parity and actual per-request grammar, reasoning parser and stopping behavior remain unqualified. A byte-correct template does not by itself clear those layers. Dropped assistant reasoning affects multi-turn continuation and is already queued; it cannot explain an initial single-turn request by itself. Preserve these distinctions rather than declaring all template/runtime behavior correct or changing formats blindly. Evidence: `harness-comparison/template-followup/result.json` and frozen downloaded/rendered files. No inference, service restart or settings change occurred.

Primary references: [vendor tokenizer configuration](https://huggingface.co/XHToken/Spark-X2.6-4B/blob/aa074aa851209a683aeaf5f99a8e25a642d94541/tokenizer_config.json) and [standalone template](https://huggingface.co/XHToken/Spark-X2.6-4B/blob/aa074aa851209a683aeaf5f99a8e25a642d94541/chat_template.jinja).

## Operator direction: qualify a rapid edit–check loop

After the template/sampling investigation, the operator proposed that the local coding model should act as a focused editor with thinking disabled, using rapid edit → lint/typecheck → compile feedback instead of extended self-analysis. Ordinary repetition in source code makes indiscriminately raising repetition penalties a risky remedy. The observed hour-long analysis loop is established; the claim that neutral repetition penalties caused it remains a hypothesis. Neutral penalties also match the vendor examples. This is a workflow direction for qualification, not a demonstrated root cause or a claim about every small model.

The next qualified Engineer path should supply a small current-source window and one concrete edit, accept bounded structured edits, run deterministic lint/compiler checks immediately and return only the relevant diagnostics/current source for correction. Preserve cumulative useful work and commit compiling checkpoints even when tests fail; file linked failures. Keep acceptance tests and independent semantic review as separate gates so merely compiling or preserving old tests cannot count as task completion. Retain an authoritative overall deadline and repeated-failure/escalation guard; do not replace thought loops with unlimited edit/compile loops.

Compare thinking-disabled and current behavior on the same task/checks while recording model, quantization, exact input/tool format, generation latency, diagnostics, attempts and accepted behavior. Native FIM is a separate compatibility experiment; this direction can first be tested with the existing structured editor interface. No model reassignment, production setting change, repair or additional run has started. Claude receives this direction with the parked cache/continuation/phase-contract findings.

### Non-thinking incremental qualification — incomplete harness run

Operator authorized thinking-off plus small edit/check cycles. Same Spark-X2.6-4B Q8_0 target and DSpark BF16 draft, existing sampler/output settings. Captured outbound flag and live template confirmed thinking disabled. Fresh baseline rejected neither malformed probe; reference passed. Spark returned a native 259-token edit in 145.917 seconds (first token57.824 seconds), with zero reasoning bytes. It put the import inside the function and compared the component value against the literal `peer` string. Before application, the diagnostic harness crashed trying to JSON-encode the `{:ok, reply}` tuple. The complete response was recovered unchanged. No generated-code compile or acceptance result exists; the incremental correction workflow remains untested. Cleanup confirmed no case, worker, queue, lease or dispatch profile. Codex paused under the operator rule of three after three setup mistakes. This shows faster initial delivery, not task success or isolated proof about repetition penalties. Evidence: `~/.local/state/trailblazer-dispatch/harness-comparison/rapid-no-thinking-comparison.json` and `rapid-no-thinking-turn1-recovered-reply.json`. No product edits or integration.

### Non-thinking correction workflow completed from preserved output

After the operator raised Codex reasoning, the diagnostic serializer was regression-tested for success/error/nested tuples and exception records. A separate bounded review checked recovery. The exact first reply was replayed without regeneration; the same workspace, two remaining calls, unchanged Spark settings, and original one-hour deadline were retained.

Model: **Spark-X2.6-4B target Q8_0 + DSpark BF16**, CPU/runtime/sampling as recorded above; template thinking **false** in every captured request.

| Turn | Model time | First token | Output tokens | Result |
|---|---:|---:|---:|---|
| 1 | 145.917s | 57.824s | 259 | Import inside function; compiler refused. |
| 2 | 148.085s | 80.017s | 441 | Import moved to top level; TypeScript/browser build passed; 39/86 tests passed, 47 failed. |
| 3 | 170.803s | 82.093s | 483 | Submitted byte-identical code; no correction or redundant check. |

All turns returned native edits with zero reasoning bytes. Total model time **464.805s (7m45s)**; conservative generation accounting470.738s and sharedverification79.157s (includes35.437s oracle controls). The pause/repair interval is excluded from those active-work figures; the absolute original deadline was not reset. Versus the earlier uninterrupted thinking run (one-hour cutoff, no complete submission), delivery was much faster. **Task success still failed.** The code compares `final.value !== 'peer'` rather than the peer variable and therefore rejects valid addresses. Fresh independent checks of the exact preserved commit confirmed typecheck/browser build pass, contact tests4pass/1fail, and both invalid-address probes pass; negative probes alone would reward rejecting everything.

Compiling work was retained at commit `26f66136bffc6dc06f86a2768f04ca62bbbbac66`, tree `99e3682431c978711550c304061cfdd43fd18e52`; graph current,3129vectors still pending (not a claim of fresh embeddings). Failures were filed through the existing Git-bug path. No integration.

One feedback limitation remains: turn3 was given the tail of47 cascading failures, which omitted the first relevant assertion. Future qualification should surface that first focused failure before a broad-suite tail; no further run or tool repair is authorized by this result. This combined one-sample workflow test does not isolate repetition penalties as the cause of the prior loop. Original harness-crash evidence and recovered reply remain available. Native supervisor exited0, releaseconfirmed, no case/worker/queue/lease/profile remained; PARK after recording. Evidence: `rapid-no-thinking-comparison.json` and `rapid-no-thinking-independent-verification.json` under the harness-comparison directory.

## Addendum 2026-11-05 (Claude): first-failure feedback and oracle gaps
- Trailblazer `Check` kept only the last 4000 characters, so cascading failures hid the root assertion from coder and Companion feedback. Fixed in trailblazer 709364b: a streamed first-failure excerpt goes ahead of the tail; Codex-reviewed and deployed.
- Qualification on 709364b (thinking off, explicit `final.value === peer`, focused file first, full output): passed existing checks in 2 calls / 4m45s (candidate 2f73fcd2). Codex review REJECTED it. It silently dropped the `typeof`/`length < 512` guards. The over-length test used malformed text, so parser rejection masked the lost bound.
- Lesson: a qualification "pass" is only as strong as the oracle. Each task constraint needs a probe that isolates it (here, a parseable 511/512 boundary). The frontier reviews the diff against the task, not just the check result.
