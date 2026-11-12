# Delivery plan, acceptance, layout, agent instructions and open decisions (README §12–16)

Moved out of `README.md` to keep it under the 500-line limit.

## 12. First-Week Delivery Plan (Target, Not a Guarantee)

**Definition of week-one success:** A person can point Resonant at an existing Git repository, provide a Markdown feature/bug specification, review a generated charter and SMART work package, execute a bounded local coding task, observe real file changes and a runner exit, see independent verification, recover from timeout/crash, integrate an approved patch, and export a traceable UAT packet. This must work without Claude, Codex, OpenRouter, or any paid API.

**Unresolved reviewer dependency:** this definition requires an operator-assigned, qualified local reviewer distinct from the Engineer. The Day 1 ADR must present the three outcomes in §16 for the operator's decision. Until that decision is recorded, do not assume a reviewer or claim that offline integration is achievable; the success definition above remains in force.

The plan is sequenced by risk and vertical completeness. Adjust daily based on actual observations. Avoid broad parallel development until the state/contract schemas are fixed. Context (Trailblazer), existing local execution capabilities, and the frontier channel (VORTEX) are adaptation candidates; discovery must establish how much execution machinery can actually be reused. Host plugins do not implement local execution. New work includes the OTP application itself, the ledger, state machines, runner/reconciler, assessment/calibration, and gates. The seven-day target remains conditional on the selected local execution adapter and demonstrated reuse; activate the named fallback (§2.2) and revise the forecast if no suitable existing adapter is established by the end of Day 1.

| Day | Primary outcome | Demonstrable acceptance |
|---|---|---|
| **1** | Discovery of owned components; ADRs (ledger, local execution adapter or named fallback, independent local reviewer, runner isolation, compute budget); project skeleton; ledger with package + attempt state machines | Transition tests for both machines; state survives restart; local execution path selected independently of host plugins by end of day; reviewer eligibility or the missing capability is recorded |
| **2** | Worktree lifecycle; cgroup-based runner with hard timeouts; reconciliation on every exit path; observability | Timeout kills the whole tree; exit never auto-accepts; silent/no-op/crash exits reconcile correctly |
| **3** | Contract intake with locked probes; Trailblazer `ContextProvider` adapter with revision stamps; selected local Engineer execution adapter; first real run | Local worker modifies an isolated worktree from revision-grounded context without a frontier client; **single-agent baseline captured** |
| **4** | Assess → commit protocol (Ready/Decompose/Clarify/Decline + timebox-only counter); calibration store and pre-assessment summary; deterministic verification + independent reviewer | Bounded assessment; counters can't touch acceptance; failed task cannot self-close |
| **5** | Diagnosis/postmortem states; PM exception handler; CLI Kanban; integration gate | PM invoked only on exceptions; accepted patch integrates with regression evidence |
| **6** | UAT export, approval tiers (CLI `decide`), restart recovery, multi-task dependency test | End-to-end offline run; recovery without duplicate work or fabricated success |
| **7** | Hardening, docs, benchmark vs baseline, usability pass, packaging | Reproducible install/demo; calibration and baseline numbers reported honestly |

### 12.1 P0: must ship for a credible system

- Elixir/OTP supervision and durable ledger, package + attempt state machines, explicit policy boundaries.
- Request → assess → commit for every substantial task, with calibration memory shown before assessment.
- Locked acceptance probes outside engineer scope.
- Git-aware worktrees, process exit/timeout detection, deterministic reconciliation.
- One working local model harness/adapter; typed task contracts.
- Context retrieval from existing indexing assets or a minimal compatible adapter with revision provenance.
- At least one independent verification path and one integration path.
- PM exception handling and one human approval workflow.
- Kanban visibility (CLI acceptable if UI time runs short) and UAT packet export.
- Recovery/integration tests and single-command local demo.

### 12.2 P1: add if the vertical slice is solid

- More specialist model adapters and capability-based dispatch within operator-approved assignments.
- Phoenix LiveView board with risk/milestone insights.
- Trailblazer adaptations beyond the minimum (richer staleness signals, test-to-code links) as Resonant usage reveals gaps.
- Additional graph/work-trace links, stronger risk scoring, more comprehensive metrics.
- Generic `HostPlugin` implementations: Claude Code and Codex first, then OpenCode, VS Code, and other clients; VORTEX is an available transport. Plugins exchange contracts, material decisions, and UAT packets without becoming local execution dependencies.
- Approach counterproposals (week one allows timebox-only counters).
- Local PM decomposition for task classes explicitly assigned by the operator after considering calibration evidence (§4.1).

### 12.3 P2: architecture must support, not necessarily implement this week

- Billion-line multi-partition index, distributed work scheduling, multi-host execution.
- Autonomous resource-market scheduling and sophisticated cost forecasts.
- Calibration-driven automatic contract sizing and timebox setting.
- Organization-wide collaboration and advanced multi-project portfolio management.

**Scope rule:** When short on time, cut breadth, not invariants. Do not replace durable state with chat context; do not disable deadlines, provenance, permission gates, or review to claim completeness.

---

## 13. Acceptance Scenarios and Required Tests

Implement these as automated integration tests where practical, plus a clearly documented end-to-end demo:

1. **Happy path:** import spec → authorize work package → local agent changes code → runner exits → Git detects change → tests and independent verifier pass → integration commit → UAT packet generated.
2. **Silent agent:** agent changes files and exits without sending any completion message. System still reconciles, verifies, and records correct status.
3. **No-op exit:** agent exits zero without changing code. Task cannot be accepted unless its acceptance evidence explicitly proves a legitimate no-change outcome.
4. **Crash after partial patch:** agent crashes with uncommitted files. Patch is preserved, linked to attempt, and sent for diagnosis or review; no false success.
5. **Hard timeout:** non-cooperative worker exceeds deadline; entire process group is ended and record contains timeout plus preserved artifacts.
6. **Missed notification:** watcher event is dropped; periodic reconciliation still detects a changed worktree.
7. **Stale context:** index describes a prior revision; runner refreshes/marks stale before relying on it and never silently claims freshness.
8. **Review failure:** independent verifier fails a criterion; engineer cannot promote status; failure is diagnosed and materially revised before retry.
9. **Concurrent tasks:** overlapping edits are isolated; conflict at integration triggers managed resolution and appropriate re-verification.
10. **Restart recovery:** OTP app or host is interrupted mid-attempt; startup reconciles leases, processes, and worktrees without duplicate work or fabricated success.
11. **Governance:** requested product-scope change becomes an approval request; no unapproved execution of the new requirement.
12. **Offline:** normal local planning/execution/test/integration uses no external API and does not require cloud credentials.
13. **Provenance:** every accepted package can be traced to requirement, contract, assessment, attempt, exact Git revision/diff, test results, and reviewer decision.
14. **Locked probes:** engineer modifies a locked probe file; hash check fails the gate and records a scope violation.
15. **Bounded assessment:** specialist counterproposes twice, or exceeds the assessment budget; PM resolves deterministically after one round.
16. **Counter can't weaken grading:** a counterproposal that alters acceptance criteria becomes an approval request, not a contract change.
17. **Unassessed path:** model returns an unparseable assessment; package proceeds as `unassessed` and calibration records the gap.
18. **Calibration loop:** a confident (≥ threshold) commitment that fails verification is recorded as an overclaim, and the next assessment prompt for that actor/task class shows the updated record.
19. **Double decline:** two eligible models decline; package escalates to the contract author rather than trying a third.
20. **Host independence and plugin parity:** the local delivery path works without any host client installed or connected. Every configured `HostPlugin` passes the same versioned contract/decision/UAT exchange and authority tests, without owning worker execution or ledger transitions.
21. **Role assignment and review independence:** retaining Spark/Bolt does not populate unassigned roles. An Engineer model cannot supply its candidate's model review through a second session or prompt. Missing reviewer capability blocks required review without promoting the candidate or silently invoking a frontier model; the offline happy path uses an eligible distinct local reviewer.
22. **Operator-owned model assignments:** calibration can recommend a model-to-role change but cannot apply it. Only a recorded operator decision changes the assignment; ordinary dispatch within that approved assignment remains autonomous.
23. **Local prompt templates:** local invocations state task, inputs, outputs, criteria, and authority/tool boundaries without assigning a persona, personal name, character, or personality, including assessment and review prompts.

24. **Bounded improvement:** a weaker or inconclusive harness candidate keeps the baseline; changed-base decisions require revalidation, and restart/cancellation cannot reset budget or duplicate adoption.
25. **Learning transfer and rollback:** retained lessons link to exact changes, matched trials, costs and counterexamples; a later package retrieves compatible evidence, reusable promotion requires independent held-out checks, and monitored regression restores the exact previous version.

**Gate:** The product is not "done" just because these cases have stories or UI mockups. Show tests, actual logs, and a reproducible demonstration.

---

## 14. Suggested Repository Layout

Moved to [docs/design/repository-layout.md](docs/design/repository-layout.md): conceptual module boundaries (core, delivery, execution, evidence, context, models, integration, interface), not a mandated tree.

---

## 15. Instructions to Codex and Claude

**Mission:** Implement SMART Resonance as a clean, future-ready greenfield Elixir/OTP application, with the strongest honest end-to-end deliverable achievable in seven days. Reuse mature code from the existing system where it demonstrably satisfies this specification. The older system is evidence of proven approaches, not the new product's architecture.

### First actions

1. Inspect the owned delivery components listed in §2.2 (Trailblazer, scout/ranger, planner, VORTEX) and confirm their current paths, versions, and protocols. Compare existing local execution capabilities, including mini-swe-agent, and select the adapter or activate the named fallback by the end of Day 1. Separately inspect the initial clients' extension interfaces for the generic `HostPlugin` port. **Do not assume** a component behaves as described here without inspection.
2. Create a concise `REUSE_DECISIONS.md` recording, per component, the adaptations Resonant needs (default: adapt; replace only with an ADR).
3. Produce an architecture ADR for the canonical work ledger, graph backend, runner isolation, and model adapter protocol. Confirm decisions against actual runtime/library compatibility.
4. Establish the schemas, state machine, and cross-component contracts *before* implementing parallel features.
5. Build and demonstrate the first vertical slice as soon as possible, then expand deliberately.
6. Maintain an honest `DELIVERY_STATUS.md` listing working functionality, test evidence, known defects, deferred functionality, and next actions.
7. Use paired programming through VORTEX, inspecting its current protocol first.

### Implementation conduct

- Prefer working code, tests, logs, and runnable instructions to extensive speculative architecture diagrams.
- Do not ask the human to micromanage ordinary design choices; decide and record them in ADRs.
- Surface truly consequential ambiguity, especially authority boundaries, resource limits, and irreversible migration.
- Do not silently change this product's high-level invariants to accelerate delivery.
- Use `mix format`, `mix test`, and appropriate static checks. Report what was **actually run** and passed.
- Never claim billion-line scalability, strong isolation, full verification, or zero costs without evidence.
- Protect existing repos and data; use new worktrees, isolated state directories, migrations/backups, and non-destructive defaults.
- Aim for offline repeatability and a single clear getting-started command.

### End-of-week deliverables

- Runnable Resonant service/CLI (and Kanban interface if practical);
- Architecture and key ADRs;
- Migration/reuse decision log;
- Example specification and complete demonstrated task run;
- Automated test suite including failure scenarios;
- Runbook and configuration guide;
- UAT evidence packet from a real run;
- Transparent known-limitations / follow-on roadmap.

**Acceptance is based on the functioning system and evidence, not on breadth of claimed features.**

---

## 16. Open Decisions to Resolve by Evidence, Not Speculation

The implementors should select reasonable technical defaults and record short ADRs for these, except decisions explicitly reserved for the operator:

1. **Canonical ledger implementation:** SQLite schema (state tables + same-transaction audit log) and migration tooling.
2. **Git-bug:** sync/export depth with the planner (the global commit-message rule already depends on it).
3. **Compute budget:** resident models, role-switch cost, realistic concurrency on the host.
4. **Model execution protocol:** selected local worker tool/execution adapter or `MinimalEngineerAdapter` fallback, role prompts, assessment prompt, and prefix-cache reuse between assessment and execution. Host plugins are outside this execution decision.
5. **Sandboxing:** cgroup-per-worker via `systemd-run --user --scope` versus a container runner; must guarantee whole-tree kill.
6. **Calibration parameters:** task-class taxonomy, confidence threshold for "confident," minimum `n` before showing numbers, decay of old records.
7. **UI:** CLI-only versus minimal LiveView during initial hardening.
8. **Parallelism:** begin conservatively; increase only with worktree conflict management and measured compute headroom.
9. **Host plugin protocol:** versioned contract/decision/UAT exchange, initial client extension interfaces, and optional VORTEX transport, without bypassing authority checks or local-only guarantees.
10. **Independent reviewer — operator decision:** the Day 1 ADR presents the outcomes below, qualification evidence, and resource implications. Candidates include LFM3-series decision models and LFM2.5-24B-A2B; no specific model is selected by this draft. Evaluate review against known faulty and correct candidates using fixed criteria and task prompts without personas, recording false acceptance/rejection and execution cost. Under `--models-max 1`, measure the complete role-switch cost: loading the reviewer, review execution, restoring the Engineer model, and resulting delay to coding work. Present those measurements with qualification results for the operator's choice.

| Reviewer ADR outcome | Required operator decision and consequence |
|---|---|
| A: Named distinct local reviewer | Operator selects a specific model, with an explicit qualification test and results. This preserves the current offline verification/integration target. |
| B: Bolt also reviews | Operator explicitly expands Bolt's assignment, supported by a reviewer qualification test. Bolt may review Spark-authored work, but cannot review a candidate authored by Bolt. |
| C: Offline work stops pending review | Operator explicitly revises week-one success: offline execution produces a candidate with mechanical checks passed, while independent review remains pending. Integration waits for authorized frontier or human review. Revise the relevant state, verification, success, and test contracts together; a pending candidate is not accepted or integrated. |

No option is selected here. A missing or expired operator decision does not choose a fallback or relax the existing gates.

11. **Model versus harness limits:** the custom pipeline's overnight failures and mini-swe-agent's EKTO hello-world success use different models and tasks, so they do not isolate either cause. Before declaring a local Engineer ceiling or investing further in the custom loop, compare the same model, serving settings, repository tasks, and independent acceptance checks across harnesses; record verified delivery, total time, steps/tokens, resource cost, and frontier intervention. Prefer the better measured approach, including replacing our harness or revising this design. See the [mini-swe-agent field note](docs/notes/2026-11-07-mini-swe-agent-evidence.md). Model assignments remain the operator's decision; this does not resume paused coding.

Graph backend and vector strategy are no longer Resonant decisions; they belong to Trailblazer (§6.3).

Do **not** turn these into obstacles to the vertical slice. Document the chosen direction and preserve reversibility where justified.

---
