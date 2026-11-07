# Overnight dispatch lessons — 2026-11-06/07

Measured input to Resonant's design from one unattended night of driving VORTEX v6 cards through the local pipeline (planner → Trailblazer → local coder/reviewer on one oneAPI llama.cpp router), with Claude as the frontier on an hourly wake. It records what happened and what it implies; it does not authorize model changes or implementation.

## Setup

- Operating rule (operator, 2026-11-06): one dispatch per card per hour; every failed dispatch gets a post-mortem (too big? underspecified? model? harness?) **and** a tool fix; at most three dispatches (~3 h) per card, then the frontier finishes the card, closes it with evidence and moves on. Cards come only from the v6 docs; the loop stops when v6 is done.
- Roles as deployed: Engineer `lucet-coder-4b-omega` Q4_K_M (Spark-X2.6-4B code fine-tune), whole-file text answers, 15-minute generation window, two tries per dispatch, no read rounds; reviewer `lfm2.5-24b-a2b:qad-review`; planner `hive-conductor-1.7b`.
- Cards: a `store.ts` move/split, an MCP protocol-version fix (audit F26), `vx uninstall`, `vx install`. Each card carried a locked acceptance test written first by the frontier.

## Scoreboard

Five cards, fifteen dispatches. **No card landed autonomously**; every card was finished by the frontier after three dispatches. Every dispatch produced a post-mortem and a harness fix (20 commits across Trailblazer and planner). The failure mix moved over the night from *format* (unreadable or misapplied answers) to *logic* (code that applies, compiles partly, and fails a type check) — the first time all night that both tries wrote real code was the dispatch after the root-cause fix below.

## Findings by class

### 1. The served chat template was the largest single cause

The coder answered every retry of four cards — and, at the end, every reply — with `<tool_call>thinking_effort…</tool_call>` and no code. Cause: lucet is served with a template borrowed from its base model; with thinking enabled, that template adds *"Now the system is invoked with `thinking_effort=high`"* to the system prompt, and the code fine-tune answers it as a tool call. Replaying the failed prompt with `enable_thinking=false` produced the file at once. A re-ask on junk replies only treated the symptom.

### 2. Context the coder cannot see, it invents

- A 766-line file (~17k tokens, most of the window at ~22 tok/s prefill) could not run at all. Fix: a **ranged view** — header, the trail's ranges, an outline of top-level declarations, real line numbers, a hard line cap; a pre-launch **window fit** check refuses cards that cannot be read in half a window.
- The ranged view first showed the trail's top ranges (a class body) instead of the declarations the request named, and the coder invented the missing `SCHEMA`. Fix: **declarations the request names come first**.
- The coder typed `parseListen()`'s result as a `String` twice because `config.ts` was not among its files. Fix: the request lists the **declaration lines of the repository names the plan uses**.
- Files shown without line numbers were edited with guessed line ranges; the host filled the missing anchors and broke a file. Fix: an unquoted range on a file shown without numbers is refused.

### 3. Output degenerates in recognizable ways

After a complete answer the coder repeated one line 347 times, or began retyping a 190-line file from its numbered view with the `145| ` prefixes copied, until the window closed and the good part was lost. Fix: the stream is stopped at a 40-fold repeat or eight consecutively numbered lines, the tail is cut, and what came before is used. It also created the same new file three times in one reply (an all-or-nothing batch then failed); identical creates now collapse.

### 4. Malformed answers: refuse with the exact form, never guess

Dialect drift was constant: `<first>…<last>whole range</replace>` with no `<replace>`, new text in a `replace="…"` attribute, a write call named `file`. Variants that carry the same meaning unambiguously (a write tool by another name, identical repeats) are accepted; anything whose intent must be guessed is refused with a message naming the problem and showing the correct form. One guessed shape would have deleted a class header.

### 5. Feedback must say why

A check step like `test $(wc -l < f) -le 640` fails with no output, and the retry was told only "failed". Fix: a silent failure re-runs the check's `test` steps under `sh -x` (bounded, no stdin) and names the failing one with its values.

### 6. Probes must cover every outcome

The F26 acceptance test covered the new module but not that both servers used it; a half-done change passed the check and only the independent reviewer caught it. Locked probes have to cover wiring, not just units.

### 7. Process lessons

- A deploy whose output is piped through `tail` hides its failure: one hot-load failed on a compiler warning and the next dispatch ran on old code. Deploy success is read from the step's own exit status and loaded-module list.
- Scope must be anchored to the design documents: a repository-wide rule (500-line files) applied literally spent effort on code that was about to be deleted.
- The three-dispatch cap worked as intended: no card ran longer than ~3 hours, and the frontier's finishing cost per card was small once the failure was understood.

## Implications for the design (applied in README)

§6.1 context packet for a non-reading Engineer; §7.1 probes cover every outcome and explain failures; §7.2 output guards; §7.5 the concrete dispatch cap and "every post-mortem ends in a harness fix"; §11 current role deployment, the served template as part of a model's identity, harness-vs-model failure attribution; §16 the Engineer capability question.

## Evidence

VORTEX cards c8047e0, 85be62a, db9b82d, 31f3d17 (post-mortem notes on each), v6-impl 323e29c…3275168. Trailblazer main f5e369c…8de4547 and planner c372539 (git-bug ids in each commit). Case streams under `~/.local/share/trailblazer/cases/vortex-*`. Running log: Claude's resume memory `project_resume_2026-11-05_vortex_dispatch.md`.
