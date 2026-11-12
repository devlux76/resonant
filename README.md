# SMART Resonance
## Greenfield Architecture and Implementation Design

**Status:** Design draft, v1.1 (not yet an implementation handoff)  
**Date:** 2026-11-03  
**Intended audience:** Codex, Claude, and human engineering reviewers  
**Target:** Once the design is accepted, deliver a working vertical slice within seven calendar days; establish the correct long-term architecture from day one.  
**Naming:** *SMART Resonance* is the product; *Resonant* is the codebase and service name.

> **Governing instruction:** This is a **greenfield architecture**, not a refactor of the current organically evolved agent system. But it is **not a greenfield codebase**: we own most of the components it needs (Trailblazer, scout/ranger, the planner, the VORTEX peer bridge). Those are **adapted and specialized behind Resonant's ports, not reimplemented.** Client/harness integrations use one generic host-plugin interface, with Claude Code and Codex first, then OpenCode, VS Code, and other clients where their extension interfaces permit. A host plugin connects top-level planning, decisions, and UAT; it is not the local execution architecture. What we do not inherit is existing coupling, data models, or control flow where they conflict with this design. Optimize for the strongest useful deliverable within the week, **without sacrificing the future architecture to achieve a demo**.

---

## 1. Executive Summary

**SMART Resonance** is a local-first, evidence-grounded software delivery system. A human and a frontier model establish the product intent and high-level specification. A locally hosted AI Delivery Manager decomposes that intent into managed, verifiable work. Specialized, empirically selected local AI workers perform analysis, design, implementation, review, integration, and diagnosis. Elixir/OTP provides reliable orchestration, supervision, timing, state control, and crash recovery. A continuously maintained source-awareness service gives each worker task-relevant evidence about the current repository. Successful local delivery culminates in a coherent build and acceptance-evidence packet for frontier-model/human UAT.

The system is not a chat-room swarm, not a set of personas applied to one generalist model, and not an endless auto-retry coding loop. It is a **managed delivery system centered on verified changes to software state**.

The core loop is:

```text
Intent → Charter → Reconnaissance → Work packages → Assess & commit → Bounded execution
     → Observe evidence → Independent verification → Integration
     → Reconcile plan → Deliver for UAT
          ↘ exception → diagnose → replan / escalate / stop ↗
```

**Foundational principle — evidence-based recursive improvement:** improve
the harness for the current work, then preserve evidence about which
changes should guide future improvement. Task-level adaptation and
learning how to improve are distinct loops, both bounded by contracts,
independent verification and existing authority. See the
[continuous-improvement design](docs/design/continuous-improvement.md).

### Product promise

- Human supplies direction rather than supervising every code edit.
- Frontier inference is reserved for high-value planning, architectural decisions, material changes, and UAT.
- Local models handle the recurring, token-intensive implementation process.
- Every agent operates with epistemic humility: claims are provisional, evidence is inspectable, and verification is independent.
- Every intelligence in the system has a voice: no specialist begins substantial work without first being asked what it thinks of the request (§4.4).
- Every intelligence also has a track record: what it said it could do versus what it actually did, kept in durable calibration memory and shown back to it (§4.5).
- Each attempt has an enforced time budget; stalled work transitions to diagnosis, not blind continuation.
- Agent memory is expendable. Institutional memory and work state are durable.
- Codebase indexing is incremental, provenance-aware, and separate from authoritative Git/project records.
- The system can be stopped and restarted without losing the ability to explain what happened or what to do next.

**Economic intent:** Avoid metered cloud inference for routine work, while recognizing local compute, electricity, and human time still have costs.

---

## 2. Architectural North Star and Greenfield Rules

### 2.1 Design the destination, then implement vertical slices

The future system, not the current implementation, defines the module boundaries. The first-week product should be the smallest complete enactment of that architecture: one real project, one real spec, one real task, one supervised local worker, one bounded attempt, one observed diff, one independent gate, one integration path, and one UAT packet.

**Do not** build a disposable mock workflow, an isolated swarm proof-of-concept, or a new UI over the old planner. **Do** build durable ports/interfaces and a tested path end to end with a small set of adapters and role-qualified models, including a reviewer distinct from the Engineer model.

### 2.2 Reuse policy: use what works, behind ports

We own the code below, but ownership and prior investment do not establish fitness. **Use what delivers the best verified results**, adapting an existing component or replacing it with a better alternative behind a Resonant port (§9). The dispositions below are starting candidates, subject to evidence; neither our harness nor this design is an end in itself. Record a replacement and its evidence in an ADR. Check each against: (1) correctness, (2) isolation, (3) observability, (4) testability, (5) local/offline operation, (6) fit to the target contracts.

| Owned component | Disposition in Resonant |
|---|---|
| **Trailblazer** (code graph + vectors + watcher; EmbeddingGemma, Tree-sitter, SQLite graph, Qdrant) | **Adapt** as the `ContextProvider` backend. Resonant consumes Trailblazer as a service; its internals (including the LadybugDB migration) stay Trailblazer's decisions, not Resonant's. Required adjustments: revision/index-generation stamps on every answer, and a staleness signal. |
| **scout / ranger** | **Adapt** as the Analyst's reconnaissance tools (fast retrieval, then local-model verification of locations). |
| Trailblazer's **file watcher** | **Adapt** as a reconcile *hint source* only, never a completion detector. |
| **Planner** (over git-bug) | **Logic donor and integration adapter.** Resonant's ledger owns work state; git-bug stays a sync/export surface so existing workflows and the commit-message rule keep working. |
| **Host plugins** | **Provision plugins behind the generic `HostPlugin` port** for contract, decision, and UAT packet exchange. Initial targets: Claude Code and Codex; later targets: OpenCode, VS Code, and other clients. Client/harness choice grants no execution or acceptance authority and introduces no delivery dependency. |
| **VORTEX** (Claude/Codex peer bridge) | **Adapt** as the frontier channel: decision packets, contract authoring, cross-review, UAT. Governed like any worker, under the same contract, audit, and evidence rules; never on the autonomous local path. |

Exact paths, versions, and protocols are verified during Day 1 discovery, not assumed from this table.

The local Engineer execution adapter is selected during discovery from working execution capabilities, including **mini-swe-agent** and Trailblazer's worktree, check, artifact, and integration machinery. The [2026-11-07 mini-swe-agent field note](docs/notes/2026-11-07-mini-swe-agent-evidence.md) makes mini-swe-agent a concrete candidate before further investment in a custom tool loop. Adapt useful capabilities behind Resonant's contracts without retaining a competing orchestration authority. Reused machinery may produce artifacts and evidence, but cannot retain completion, verification, acceptance, or work-status authority: Resonant's control plane applies independent verifier evidence and authorized decisions to its ledger. This selection does not depend on a host plugin.

**Named fallback: `MinimalEngineerAdapter`.** If discovery has not established a suitable existing execution adapter by the end of Day 1, select this fallback in an ADR: a minimal in-tree tool loop against the local llama.cpp-compatible endpoint, supporting worktree-scoped read/search/edit and policy-authorized commands. It uses Resonant's runner for its cgroup, deadline, permissions, and evidence capture; it adds no scheduler, retry policy, ledger, or acceptance authority. Reuse compatible tool primitives where available. Activating the fallback requires revisiting the seven-day forecast against its implementation and verification work.

### 2.3 Non-negotiable invariants

1. **Git is authoritative for source content**; a transactional project ledger is authoritative for work status and decisions. Indexes are rebuildable projections.
2. **No model can mark its own work verified or integrated.**
3. **Agent exit does not mean task success.** A watcher event does not mean task success.
4. **No unbounded retry, token consumption, elapsed execution, or context growth.**
5. **No silent scope changes.** Material changes require governed approval.
6. **Every meaningful claim has a traceable evidence source**, or is explicitly marked uncertain.
7. **Every execution is tied to a baseline revision, attempt ID, workspace, timebox, and declared authority.**
8. **Elixir controls orchestration**; specialist subprocesses can crash without corrupting authoritative state.
9. **Offline/local is the default**, with explicit opt-in for cloud/frontier calls and external network access.
10. **The human retains product authority**, including final acceptance and decisions that change intended scope.
11. **Improvement earns adoption:** compare against a preserved baseline, retain supporting and negative evidence, test transfer before reusable promotion, and make keep-baseline and rollback ordinary outcomes (§2.4).

### 2.4 Continuous improvement is part of delivery

[Evidence-based recursive improvement](docs/design/continuous-improvement.md)
extends the existing workflow rather than creating another orchestrator.
Calibration tracks actors; the improvement record links specific harness
changes to matched executions, costs, scope and counterexamples. The
first slice captures that evidence; generalized optimization follows
proved execution and verification. This design addition does not resume
paused implementation or authorize new models, roles or external calls.

---

## 3. System Boundaries and High-Level Topology

```mermaid
flowchart TD
    H[Human Product Owner] <--> F[Frontier Planning / Architecture / UAT]
    F --> I[Spec Intake and Governance]
    I --> PM[Local AI Delivery Manager]
    PM <--> W[Durable Workflow Engine / Kanban]
    W <--> L[(Transactional Project Ledger)]
    W --> S[Scheduler / Leases / Timers]
    S --> R[Supervised Worker Runners]
    R --> A[Analyst / Designer / Engineer / Verifier / Integrator / Diagnostician]
    A <--> C[Task Context Service]
    G[(Git / Worktrees)] --> X[Watcher + Reconciler]
    X --> IDX[Incremental Source Indexer]
    IDX --> V[(Vector Index)]
    IDX --> GR[(Source Graph)]
    C --> V
    C --> GR
    C --> G
    R --> G
    X --> W
    W --> PM
    W --> U[UAT Evidence Packet]
    U --> F
    F --> H
```

### 3.1 Control plane vs intelligence plane

**Control plane (deterministic Elixir/OTP):** workflow transitions, leases, timers, process lifecycle, policy enforcement, worktree ownership, concurrency locks, event recording, retries, cancellation, reconciliation, and evidence collection.

**Intelligence plane (model-backed):** interpreting intent, planning work, source investigation, task design, code generation, semantic review, diagnosis, risk assessment, and exception decisions.

**Knowledge plane (derived, rebuildable):** embeddings, parsed symbols, source graph, code/doc links, task context trails, test-to-code relationships where known.

**Evidence plane (durable, auditable):** commits/diffs, test output, execution metadata, decisions, approvals, source revisions, artifact hashes, and verified acceptance records.

The delivery manager makes judgments. It is **not** the scheduler, persistence engine, process supervisor, source of truth, or sole gatekeeper for completion.

### 3.2 Deployment target

Single-host, local-first deployment initially; multiple hosts and model backends later. Elixir/OTP is the long-lived coordinator. External model runtimes and native-risk engines operate as supervised subprocesses or services with explicit health checks. Do not assume BEAM supervision alone handles grandchildren or OS process trees; the runner must implement reliable subprocess-tree termination. Recommended default on Linux: launch each worker in its own cgroup (e.g. `systemd-run --user --scope`) and kill the cgroup. This catches descendants that escape the process group via `setsid`, and provides memory/CPU limits in the same mechanism.

**Compute budget is a first-class design input.** Inference capacity on the host determines how many models can stay resident, whether role switches cost a model reload, and whether parallel worktrees help at all. Preserve the current screened allocation as the starting point: Spark for coding on its CPU serving path and Bolt for bounded planning decisions on its separate iGPU serving path (§11). The Day 1 ADR records that deployment and its actual resource budget; measure contention before changing placement or concurrency, with mechanical checks as the primary gate.

Keep transport stable and versioned (JSON over local HTTP, Unix socket, or stdio, selected by implementation assessment). Prefer a simple, documented API over elaborate distributed messaging during week one.

---

## 4. Roles, Authorities, and Separation of Duties

Roles are **capability contracts**, not personalities or permanently running processes. Deterministic responsibilities require no model. The operator owns model-to-role assignments; eligibility means an operator-approved assignment, not an inference from calibration scores. Dispatch work within those assignments using measured task performance and resource constraints. A model may hold multiple operator-approved roles, but cannot provide the model review of a candidate it authored; the reviewer must be a different model (§8.1), with independent context and evidence.

| Role | Responsibility | Inputs | Outputs | Forbidden actions |
|---|---|---|---|---|
| Human Product Owner | Establish and change intended outcomes; final accept/reject | Vision, material tradeoffs, UAT results | Intent, approval, acceptance | N/A |
| Frontier Architect / Sponsor | High-level spec, architectural decisions, material-change review, UAT assistance | Human direction, decision packets, delivery evidence | Spec, approved decisions, assessment | Unapproved changes to human product intent |
| **Delivery Manager (PM)** | Charter, decomposition, sequencing, resource allocation, risk/issue/change management, delivery forecast, exceptions | Spec, task ledger, dependencies, evidence summaries | Work packages, priorities, authorizations, escalation packets | Self-verifying implementation, silent scope changes |
| Analyst | Reconnaissance, requirement-to-code mapping, risk/unknown discovery | Task, indexed trail, source revision | Findings with citations, unknowns, impact map | Claiming unverified runtime behavior as fact |
| Designer | Detailed design and SMART task contracts | Charter, analyst findings, constraints | Acceptance criteria, implementation contract, test plan | Changing product requirements without approval |
| Engineer | Implement changes in assigned sandbox/worktree | Contract, evidence trail, tool access | Changed files, optional status notes, reproducible diff | Self-approval, modifying unauthorized paths |
| Verifier | Independently validate behavior, quality, security and regression risk | Contract, diff, test environment, evidence | Pass/fail/uncertain report with reproducible proof | Editing candidate implementation while reviewing it |
| Integrator | Conflict handling, controlled merge, integration tests, delivery assembly | Accepted changes and baseline | Integration revision and test report | Overriding failed gates without recorded authority |
| Diagnostician | Timeout/failure/postmortem and next-step hypotheses | Attempts, logs, diffs, test outputs, context | Root-cause hypothesis, evidence, bounded remediation proposal | Repeating failed attempt unchanged |

**Optional specialist capabilities** are plugins to these roles: security reviewer, UX reviewer, database migration specialist, performance profiler, documentation writer, dependency auditor, etc. Avoid creating a model-led management hierarchy for each specialty.

### 4.1 Delivery Manager: PMI-inspired operating model

- **Initiate:** create a project charter: objectives, deliverables, acceptance, scope exclusions, authority, resources, risks, constraints.
- **Plan:** deliverable-oriented work breakdown structure, dependency network, milestones, resource limits, critical path / bottleneck identification, SMART work packages.
- **Execute:** delegate work, not implementation minutiae; manage assignments, priorities, and blocked dependencies.
- **Monitor/control:** manage by exception, compare observed evidence with acceptance criteria and plan, forecast outcomes, handle risk and changes.
- **Close:** prepare UAT packet, obtain acceptance, close open work honestly, capture lessons learned.

Use a **hybrid/adaptive process**, not a mandatory sequential waterfall. Within a package, specialists may iterate, but every attempt has explicit bounds and every accepted state is evidence-based.

**Who writes contracts, initially.** Decomposition into SMART contracts is the highest-leverage judgment in the system. Initially, the frontier architect performs the Designer/contract-authoring role through `HostPlugin`; already approved contracts can also be imported for offline execution. The local PM handles sequencing and exceptions. Local contract authoring is a separately qualified, operator-assigned capability, not a default Spark/Bolt assignment. Calibration (§4.5) may support a proposal for the local PM to take over decomposition for a task class; the operator decides whether to authorize that assignment.

### 4.2 Management by exception

Routine, within-tolerance events do not require PM inference. Wake the PM only for decision-worthy situations, such as failed acceptance gates, repeated timeout, blocked critical dependency, conflicting changes, milestone forecast breach, insufficient resources, or a potential scope deviation. The workflow engine handles normal transitions deterministically.

### 4.3 Approval tiers

| Tier | Example | Decision owner |
|---|---|---|
| 0: Routine | Retry infrastructure error once under policy, run tests, reindex | Workflow engine |
| 1: Within contract | Select implementation, reorganize tasks, adjust sequencing | Specialist or PM as delegated |
| 2: Architectural | Break approved architectural constraint, choose major substitution | Frontier architect with recorded decision |
| 3: Product | Alter intended behavior, delivery scope, security/privacy promise, accepted tradeoff | Human, optionally advised by frontier architect |

Explicitly store approver, decision, rationale, affected requirements, and revised baseline. If frontier access is unavailable, pause only work that truly requires that approval; continue unrelated work. Approval requests carry an expiry; an expired request returns the package to the PM as an exception rather than waiting indefinitely.

Model assignment, role expansion, and model replacement are operator decisions, recorded in the ledger and capability registry. Calibration supplies evidence and recommendations; it never hires, promotes, demotes, or replaces a model automatically. Ordinary task dispatch and within-contract implementation choices remain delegated within the approved assignments.

### 4.4 Every intelligence has a voice: Request → Assess → Commit

No model-backed specialist begins substantial work until it has been asked what it thinks of the request and an explicit commitment has been recorded. Deterministic duties do not require a model or an assessment call. The PM does not issue "implement X in 20 minutes." It says: here is the outcome, the context, the acceptance criteria, and a proposed timebox — what do you think, and are you prepared to take this on?

This is deliberate, and it mirrors how the human works with Claude and Codex: asking "what do you think of this design, would you implement it as is?" rather than "implement file X." In practice, intelligences that are given autonomy over whether and how to take on work produce better first-attempt results. The voice is the point; the calibration record (§4.5) is what keeps the voice honest.

**Protocol:**

1. **Request:** PM (or contract author) proposes outcome, context packet, acceptance criteria, and timebox.
2. **Show the track record:** the specialist's calibration summary for this task class is placed in front of it *before* it assesses (§4.5).
3. **Assess:** the specialist examines feasibility, uncertainty, dependencies, and its own fit.
4. **Respond** with one of:

| Response | Meaning | Next step |
|---|---|---|
| Ready | I expect to deliver within these constraints | Commit as proposed |
| Counterproposal | I can do this with a different timebox or approach | PM accepts or rejects (one round) |
| Decompose | This should be split into smaller verifiable goals | Back to contract author |
| Clarify | Important information is missing | Back to contract author, with the questions |
| Decline | I am not the right choice for this | Re-route to another eligible model |

5. **Commit:** the agreed contract (proposal + assessment + any accepted counter) is hashed and recorded; execution begins.
6. **Review:** independent evidence, not the commitment, determines whether the work was delivered.

Every response also carries a structured estimate: `expected_minutes`, `confidence` (0–1), and `risks`. These feed calibration.

**Declining is not failure.** A specialist that recognizes a poor fit before spending the execution budget has saved the organization time. Declines are never penalized in routing; only *mis-calibration* is (§4.5).

**Bounds (so deliberation can't become the new infinite loop):**

- Assessment has its own small time and token budget. Run it in the same model session as execution where possible (context packet first, then the assessment prompt, then the committed contract), so the prefix cache is shared and assessment costs one short generation.
- At most **one** counterproposal round. After it, the PM deterministically accepts, re-routes, or returns the package to the contract author.
- Declines from two eligible models mean the contract is the problem: escalate to the contract author instead of trying a third model.
- A counterproposal on timebox or approach is Tier 1 (PM decides). A counterproposal that touches acceptance criteria, locked probes, or scope goes through the approval tiers like any other change. Negotiation can never weaken the work's own grading.
- An unparseable or missing assessment is recorded as **unassessed**; the PM's proposal proceeds. Consistent with §7.2, model output formats are hints, not control-plane prerequisites.

**Reciprocity.** The PM must be prepared to change its plan when a specialist supplies better information. The manager can be wrong about the task, the specialist about its capabilities, the verifier about the implementation. Every claim stays open to evidence.

### 4.5 Calibration memory: every voice has a track record

Self-assessment is weak evidence, especially from small local models, which lean toward "Ready." The fix is not to silence the voice but to give it, and the PM, a memory of how past assessments turned out.

**What is recorded** (durable, in the ledger, per assessment): actor (model + harness + prompt-template version), role, task class, response, stated confidence, estimate, risks named, and once known, the verified outcome and actual time.

**Outcome quadrants** for each committed attempt:

| | Verified success | Verified failure |
|---|---|---|
| **Confident** (Ready, confidence ≥ threshold) | calibrated | **overclaim** |
| **Uncertain** (low confidence or risks flagged) | underclaim | calibrated warning |

Declines are tracked separately: a decline followed by another model succeeding on the same contract is a possible underclaim; a decline followed by Decompose/Clarify being validated is a good call.

**Derived scores** per actor × role × task class:

- success rate with sample size (`n`);
- overclaim rate and underclaim rate;
- calibration error (e.g. Brier score of stated confidence vs outcome);
- estimate ratio (actual time / estimated time), which also calibrates future timeboxes;
- whether named risks were the ones that actually caused failure.

**Shown before assessment.** The specialist sees its own record — the "yeah, but…" — before it answers:

> Your record on `elixir/test-fix` tasks: 9 committed, 6 verified successes. 2 of the 3 failures were stated at confidence ≥ 0.8. Your estimates run 1.6× short on average.

This is information, not punishment. Below a minimum sample (initially `n < 5`) the summary says "insufficient history" instead of a number.

**Used by the PM.** Dispatch within operator-approved assignments, timeboxes, and contract size use the record, not the stated confidence alone. A frequent overclaimer may need smaller contracts and longer timeboxes; a frequent underclaimer may need better context or a different decomposition. Model-to-role changes are recommendations to the operator, never automatic assignments or replacements.

**Applies to everyone, including the PM and the verifier.** The PM's estimates and contracts get a record (contracts later sent back for Decompose/Clarify, forecasts missed). The verifier gets one too: passes later contradicted at integration or UAT count as overclaims. Reciprocal humility requires that the manager is measured too.

Calibration memory is **institutional memory**, not agent memory: it survives model restarts and context loss, and it is keyed by model *and* prompt-template version so a prompt change starts a fresh record.

---

## 5. Durable Domain Model

Use a transactional project store (SQLite with WAL is acceptable for single-host launch) behind repository interfaces. Current state lives in state tables; every transition appends an audit event **in the same transaction**. This is state + audit log, not event sourcing: events explain history but are not replayed to derive state. All transitions are atomic and idempotent. Never treat a prompt history as the authoritative work ledger.

**Entities (minimum useful schema):**

- `Project`: ID, repo root, charter version, accepted spec revision, status, privacy policy, resource limits.
- `Requirement`: stable ID, text, source, acceptance criteria, priority, version, approval state.
- `Milestone`: deadline/target, included deliverables, dependencies, acceptance and forecast.
- `WorkPackage`: ID, goal, dependencies, owner role, expected outputs, authorized scope, acceptance contract, time budget, status, priority, relevant requirement IDs.
- `Attempt`: ID, package ID, model/role/harness, task contract hash, baseline Git revision, worktree ID, start/deadline/finish, exit reason, tokens/compute estimates if available, evidence links.
- `Evidence`: type, source path/commit/test run, content hash, index generation, timestamp, assertion supported, confidence or uncertainty, producer.
- `Decision`: authority tier, proposal, options, rationale, approving actor, timestamp, impact, spec revision.
- `RiskIssue`: risk vs realized issue, owner, probability/impact or severity, trigger, mitigation, linked packages, status.
- `Event`: unique ID, aggregate, sequence/version, timestamp, cause, payload, correlation and idempotency keys.
- `ModelCapability`: model/harness ID, supported role/task types, operator-approved assignments and decision references, context limits, memory/compute needs, local endpoint, quality and reliability observations.
- `Assessment`: package ID, actor (model + harness + prompt-template version), role, task class, request hash, response (ready/counterproposal/decompose/clarify/decline/unassessed), confidence, estimate, risks, counterproposal body, PM disposition.
- `CalibrationRecord`: derived per actor × role × task class: n, success rate, overclaim/underclaim rates, calibration error, estimate ratio. Rebuildable from `Assessment` + `Attempt` outcomes, so it is a projection, not a second source of truth.
- `WorkspaceLease`: task/worktree, lock scope, lease owner, lease expiry, fencing generation.

**Relationships:** requirement → deliverables → packages → assessments → attempts → code/test evidence → decisions → acceptance. Preserve this lineage in the ledger even if the knowledge graph is later rebuilt.

### 5.1 State machines: package and attempt are separate

A work package and an attempt have different lifecycles. A package can be IN_PROGRESS while attempt 2 of 3 is running; conflating them makes that unrepresentable. The **package machine** reacts to **attempt outcomes**; attempts never change package state directly.

**Work package:**

| From | To | Trigger |
|---|---|---|
| PROPOSED | REFINED | contract drafted with locked probes |
| REFINED | ASSESSING | request sent to an eligible specialist (§4.4) |
| ASSESSING | COMMITTED | Ready, accepted counterproposal, or unassessed |
| ASSESSING | ASSESSING | Decline → re-route to the next eligible model (max 2 models) |
| ASSESSING | REFINED | Decompose / Clarify, or two declines |
| COMMITTED | IN_PROGRESS | attempt created |
| IN_PROGRESS | REVIEW_READY | attempt ends `SUCCEEDED_CANDIDATE` and mechanical checks pass |
| IN_PROGRESS | DIAGNOSIS | attempt ends `INCOMPLETE` / `FAILED`, or mechanical checks fail |
| REVIEW_READY | VERIFYING | independent verifier assigned |
| VERIFYING | ACCEPTED / DIAGNOSIS | verification pass / fail or uncertain-beyond-tolerance |
| ACCEPTED | INTEGRATING → INTEGRATED → CLOSED | serialized integration, regression evidence recorded |
| INTEGRATING | DIAGNOSIS | conflict or regression failure |
| DIAGNOSIS | COMMITTED | materially revised contract or inputs, attempts remaining |
| DIAGNOSIS | REFINED | contract problem (decompose / clarify) |
| DIAGNOSIS | ESCALATED | attempt cap reached, or Tier 2/3 decision needed |
| ESCALATED | REFINED / COMMITTED / CANCELLED | recorded decision |
| any non-terminal | BLOCKED → prior state | dependency or approval wait, then cleared |
| any non-terminal | CANCELLED | authorized human/PM action |

Terminal: CLOSED, CANCELLED.

**Attempt:**

```text
CREATED → LEASED → RUNNING → RECONCILING → SUCCEEDED_CANDIDATE | INCOMPLETE | FAILED
                      │
                      ├─ deadline → TERMINATING → RECONCILING (exit_reason = timeout)
                      └─ crash    → RECONCILING (exit_reason = crash)
```

`SUCCEEDED_CANDIDATE` only means "produced a diff worth verifying"; it never implies acceptance. An attempt **always** passes through RECONCILING, whatever the exit path. When a package goes BLOCKED or CANCELLED with a live attempt, the attempt is cancelled through the normal TERMINATING → RECONCILING path and its workspace preserved.

Implement both as table-driven state machines with authorization checks, and append the transition audit event in the same transaction as the state change. A package is only **CLOSED** after integration and required evidence, not because a process exited.

### 5.2 Event semantics

File events and embedding/graph notifications are **hints to reconcile**, not durable proof of completion. Prefer delivery of hints with at-least-once semantics and idempotent handling. Periodic scanning of relevant Git/worktree state and pending records repairs missed events. Where no worktree file changes occur, an attempt may still terminate and produce logs, analysis, or a legitimate `BLOCKED` outcome; the runner must always reconcile on exit/timeout.

Record baseline revision, observed worktree fingerprints (including untracked files), diff/patch hash, and index generation. If the index lags the worktree, context must either wait for a consistent generation or mark staleness and retrieve directly from Git/files with provenance.

---

## 6. Source Awareness and Task Context

### 6.1 Desired context architecture

Source awareness is **Trailblazer's job**; Resonant consumes it through `ContextProvider`. Trailblazer already combines EmbeddingGemma embeddings, Tree-sitter parsing, a relationship graph, and vector search, with scout/ranger for retrieval and verification. Resonant does not rewire those internals. It specifies what it needs from them (below) and adapts Trailblazer where it falls short. Keep source graph and work ledger logically distinct even when interoperable.

**What Resonant requires of each answer** (Trailblazer adaptations where missing): repository and revision, path, language, AST node/symbol, byte/line span, content hash, embedding version, provenance, index generation, relationships (import/call/define/reference/test/doc when supported), parse/index status, confidence.

**Retrieval flow:**

1. Extract task objective and accepted constraints.
2. Retrieve relevant docs/code semantically.
3. Expand bounded structural neighbors (three hops is an initial policy, not a hard architectural limit; apply fanout and token budgets).
4. Add authoritative recent diffs, test results, and unresolved linked issues.
5. Rank for evidence quality and task relevance; deduplicate.
6. Return a compact typed **context packet** with navigable references and explicitly marked unknowns.
7. Refresh or invalidate packet if baseline/index generations diverge.

The index need not infer complete runtime behavior. It must instead distinguish directly observed syntax, inferred semantics, and unverified assumptions.

**Packet for an Engineer that cannot read** (measured 2026-11-07, [overnight note](docs/notes/2026-11-07-overnight-dispatch-lessons.md)): what a small coder is not shown, it invents. The packet therefore carries (a) the declaration lines of every repository name the contract mentions; (b) for a file too long to send whole, a *ranged view* with real line numbers — header, the declarations the request names first, then the trail's ranges, plus an outline of the rest — under a hard line cap; (c) line numbers only where the Engineer may edit by line range. Before dispatch, refuse a package whose packet cannot be read in half the generation window; split it instead. Prompt size, not model choice, is the first lever on local prefill.

### 6.2 Scale design

The architecture must accommodate enormous, messy, pre-existing repositories eventually. Achieve this with incremental ingestion keyed by content hash, bounded batch sizes, backpressure, sharding/partition boundaries, selective graph traversal, index-version metadata, and the ability to rehydrate from Git. **Do not promise full billion-line indexing in week one.** Demonstrate correct incremental updates, changed-file invalidation, and retrieval provenance on a representative repository.

### 6.3 Graph backend (Ladybug) belongs to Trailblazer

Trailblazer's planned move to LadybugDB is Trailblazer's decision and does not block Resonant: Resonant depends only on `ContextProvider`. Constraints that matter if Trailblazer adopts it: Ladybug permits one active read-write `Database` per file, so Trailblazer needs a single graph owner; and an embedded NIF risks crashing the BEAM, so if Resonant ever hosts the graph in-VM, prefer a supervised sidecar.

Source: https://docs.ladybugdb.com/concurrency/ ; https://ladybug-ex.hexdocs.pm/readme.html

---

## 7. SMART Work Contracts and Agent Execution

### 7.1 Contract validation

Each work package must satisfy:

- **Specific:** one observable outcome and explicitly authorized file/system scope.
- **Measurable:** programmatic checks wherever practical, plus explicit review questions.
- **Achievable:** reasonable relative to assigned model, environment, dependency state, and timebox.
- **Relevant:** links to a parent deliverable/requirement.
- **Time-bound:** separate per-attempt execution budget and project milestone target.

**Acceptance probes are locked.** Probes are authored with the contract (by the contract author or Designer), before implementation, stored **outside** the engineer's `allowed_paths`, and pinned by content hash in the contract. The engineer may add tests of its own, but the gate runs the locked probes. An engineer that can edit the tests that grade it has nothing to be graded against.

Probes cover **every** outcome in the objective, including wiring, not only the new unit: a probe that a half-done change passes defers the catch to review (F26, 2026-11-07). A probe that fails must say why; a silent step (`test …`) is explained by the harness from its expanded values, never reported as a bare "failed".

No execution begins without a valid, **committed** contract (§4.4) and available dependencies. When tests cannot conclusively prove a property, record the verification method and residual uncertainty rather than inventing certainty.

Example:

```yaml
schema_version: 1
id: AUTH-042
requirement_ids: [REQ-AUTH-3]
objective: "Reject expired tokens without changing valid-token behavior"
role: engineer
baseline_revision: "<full-git-sha>"
workspace: "worktrees/AUTH-042"
timebox_seconds: 1200        # illustrative; calibrate using observed history
allowed_paths:
  - "src/auth/**"
  - "test/auth/**"            # engineer may add its own tests here
acceptance:
  - id: A1
    type: command
    run: "mix test .resonant/probes/AUTH-042/expired_token_test.exs test/auth"
    locked_files:            # outside allowed_paths; hash-checked before the gate runs
      - path: ".resonant/probes/AUTH-042/expired_token_test.exs"
        sha256: "<hash>"
    expectation: "exit_code == 0"
  - id: A2
    type: review
    expectation: "Expired token rejected; valid token still accepted"
commitment:                  # filled by §4.4; part of the contract hash
  assessed_by: "<model>@<harness>/<prompt-template-version>"
  response: ready             # or counterproposal (accepted), unassessed
  confidence: 0.7
  expected_minutes: 15
  risks: ["token clock skew handling unclear"]
constraints:
  - "No public API changes"
  - "No network access"
escalate_if:
  - "Public API change is required"
  - "Acceptance criteria cannot be demonstrated"
```

Paths and commands above are illustrative, not assertions about the actual project stack.

### 7.2 One attempt, one bounded responsibility

The runner must:

1. Start from a **committed** contract (§4.4): model/harness selected within an operator-approved assignment and available resources, with the assessment recorded. Calibration can inform dispatch but cannot grant a new model role.
2. Obtain an atomic lease and task-specific worktree tied to the baseline.
3. Assemble a revision-stamped context packet and render a concise task prompt (reusing the assessment session's prefix where possible): task, inputs, expected outputs, acceptance criteria, and authority/tool boundaries. Local prompt templates assign no persona, personal name, character, or personality. Role names identify responsibilities and permissions, not characters to enact; assessment and calibration use the same task-focused format. This local-model rule does not prescribe the framing used by frontier hosts.
4. Launch the worker under supervision, in its own cgroup, with permission/network/resource limits.
5. Enforce a real deadline external to the model. On expiry, request graceful cancellation then forcibly terminate the process group after a short grace period.
6. Always capture exit information and reconcile Git state, including uncommitted and untracked changes.
7. Execute deterministic acceptance probes when safe; submit artifacts for independent verification.
8. Persist outputs/links and release the lease, or enter failure diagnosis.

**Output guards.** The runner reads the stream, not just the final reply: degenerate repetition (one line repeated tens of times) and a numbered source view copied back are stopped at once and cut, and the complete answer before them is used. Answer-format variants that carry the same meaning unambiguously (a write tool under another name, identical repeated blocks) are accepted; a shape whose intent must be guessed is refused with the exact correct form, never repaired by inference.

Structured agent status messages are **optional hints**, not control-plane prerequisites. This is deliberate: smaller local models may omit tool calls or fail to report completion reliably at long context lengths.

### 7.3 Timebox vs milestone

A failed attempt deadline does not automatically imply missed delivery. A collection of successful attempts does not automatically imply milestone progress. Track execution limits and delivery forecast separately. Preserve partial useful work across attempts only after reconciliation and explicit selection; don't pass an agent's unsupported claims forward as facts.

### 7.4 Epistemic humility as protocol

Workers must produce or be assigned a reviewable handoff:

- `observed`: what was actually changed/tested and how known;
- `inferred`: reasoned conclusions not directly demonstrated;
- `unknown`: unresolved assumptions or missing evidence;
- `artifacts`: paths, diffs, commands, results;
- `request`: verification or decision needed.

Do not depend on the agent obeying this output format. Derive observed fields from tools wherever possible. Unsupported model statements remain unverified claims.

### 7.5 Failure and postmortem policy

On timeout, crash, failed test, review rejection, or repeated low-progress attempts:

- Stop the attempt, preserve workspace, record a complete failure envelope.
- Run automatic classification (infrastructure/transient, stale context, mistaken assumption, unmet dependency, model-capability mismatch, design/spec ambiguity, quality failure, resource shortfall).
- Invoke Diagnostician only when deterministic classification is insufficient.
- Permit one low-risk retry for demonstrated transient infrastructure failure by policy. Otherwise require a materially changed plan, input evidence, model, subtask boundary, or dependency state before retry.
- Cap total attempts per work package and escalate when exhausted. Operating default (operator, 2026-11-06): one-hour dispatches, at most three per package (~3 h); after the third the frontier finishes the package, closes it with evidence and moves on — never a fourth dispatch.
- Every post-mortem ends in a **harness/tool fix** for what it exposed, validated on the next package, including the post-mortem before a frontier finish: the goal is a pipeline that stops needing the finish, not only the package delivered.
- For repeated failure, explicitly compare attempts and prevent an identical command/prompt strategy loop.

Deadline extensions must be recorded, justified, and authorized; do not silently reset the clock to hide overrun.

---

## 8. Review, Integration, Change Control, and UAT

### 8.1 Verification pyramid

1. **Mechanical checks:** changed-file/scope guard, parse/compile, formatting where relevant, test execution, forbidden-path or network violation, diff integrity.
2. **Independent model review:** task contract vs observed code, edge cases, unsupported assertions, risk of regression.
3. **Integration checks:** merged revision, full relevant tests, migration compatibility, interface behavior, reproducible build.
4. **UAT:** stakeholder-facing acceptance against charter and requirements; the frontier architect assists but the human authorizes final acceptance.

The Verifier receives the contract and evidence without inheriting the Engineer's entire thought history. Separate worktrees/permissions; reviewer cannot change the candidate patch as part of approving it. Locked probes are the primary gate. The model reviewer must have a different model identity from the attempt's Engineer; changing only the session or prompt does not qualify. Use a separately qualified local reviewer, or explicitly authorized frontier review through `HostPlugin`. If no eligible independent reviewer is available, keep the required review blocked or uncertain; never substitute Engineer self-review or silently call the frontier. A different model does not replace the mechanical checks or establish correctness by itself.

### 8.2 Isolation and concurrency

- Assign isolated worktrees to concurrent implementation packages.
- Require lease/fencing for conflicting paths or shared resources. Path overlap is a warning, not the only conflict detector; interface/API changes may have broader impact.
- Integration is serialized per target branch. Merge/rebase conflicts are reviewed and tested, never silently discarded.
- Worktree cleanup must preserve failed/partial work until its disposition has been recorded.
- If base revision changes, revalidate affected evidence and run appropriate regression checks.

### 8.3 Governance

Use an integrated change record: original requirement, proposed change, reason, affected scope, schedule/quality/security/architecture implications, authority tier, decision, and new baseline. When a frontier or human decision is required, create a **concise decision packet**, not a full transcript dump. Frontier involvement must be explicit and bounded; do not automatically exfiltrate code or proprietary data.

### 8.4 Delivery packet

A project is ready for UAT when it has:

- Target revision and runnable/build instructions;
- Requirements-to-evidence traceability;
- Accepted and deferred work, with known defects;
- Relevant test results and limitations;
- Architecture decisions and authorized deviations;
- Reproducible steps for the human/frontier reviewer;
- Clear acceptance questions, not merely "all tasks done."

The packet should be a Markdown artifact plus machine-readable manifest. UAT rejection creates new or revised work packages rather than silently rewriting historical acceptance records.

---

## 9. Interfaces and Extensibility

Build minimal but explicit ports, with working test doubles:

```text
ProjectStore           create/read/transition/append_event
GitWorkspace           create/baseline/diff/status/commit/merge/cleanup
WorkerRunner           start/cancel/terminate/inspect
ModelRegistry          assigned_models/recommend/record_assignment/record_outcome
AssessmentService      request_assessment/record_response/resolve_counter
CalibrationStore       record/summary_for(actor, role, task_class)/rebuild
ContextProvider        build_packet/invalidate/verify_freshness   (Trailblazer adapter)
VerificationRunner     run_checks/verify_locked_probes/collect_results
PolicyEngine           authorize_transition/authorize_change
ApprovalGateway        request_decision/record_decision
HostPlugin             exchange_contract/exchange_decision/exchange_uat/exchange_status
EventPublisher         publish_reconcile_hint
```

Embedding, vector, graph, and parsing concerns are behind `ContextProvider` inside Trailblazer, not separate Resonant ports.

Use typed request/response contracts, stable IDs, version fields, idempotency keys, and explicit error types. Internal ports need not be remote services. Build a modular monolith first, with **separately supervised external workers** where needed; don't preemptively create dozens of services.

**Worker adapters:** local model roles use bounded execution adapters backed by their role-qualified local endpoints. The Engineer's tool and execution adapter is selected from existing capabilities, including mini-swe-agent, during discovery, with `MinimalEngineerAdapter` as the bounded fallback (§2.2); the runner owns the attempt workspace, deadline, permissions, cancellation, and evidence capture. Analyst, Verifier, and an escalated Diagnostician may call their separately assigned model directly where no tool loop is needed (§11). Deterministic integration and failure classification need no model. No local worker role depends on a host plugin.

**Client/harness integrations:** `HostPlugin` is a versioned packet-exchange port for intent refinement, contract authoring, architectural decisions, and UAT. Implement Claude Code and Codex plugins first, then OpenCode, VS Code, and other clients as their extension interfaces permit; ChatGPT can participate through a compatible integration. The host is distinct from the model and its assigned role. Every plugin exchanges the same typed contracts, decision packets, decisions, and delivery evidence, without owning execution or work state. `HostPlugin` handles transport; `ApprovalGateway` records decisions and `PolicyEngine` enforces their authority. Model capability and authority are independent of the client/harness. VORTEX remains an available transport under the same contract, audit, permission, and evidence rules. Host integrations are optional to autonomous local operation, which must work without any host client installed or connected.

**Caller status updates:** The host plugin must regularly update the caller with the current status while delegated work is running, and directly notify the originating conversation on completion, failure, or a required decision. Include the sender, work ID, current stage, and a reference to the evidence; use recorded state rather than the worker's unsupported claims. Direct notifications should let the caller respond without waiting for a periodic check, where the host supports that delivery route.

Example, following Trailblazer's direct notification to Codex: “From Resonant: package X is ready for review. Compilation passed; the checkpoint and test results are saved with the linked evidence. No integration or push occurred.”

**Initial interface:** usable CLI plus a simple Kanban view, preferably Phoenix LiveView if already efficient in the chosen Elixir project structure. UI is a projection of the ledger, not another authority. Show project/milestone state, task state, assigned role, remaining time, latest evidence, blocker/exception, and approval actions.

**Observability:** structured events and correlation IDs per project/package/attempt; local logs and trace views; compact performance counters. Avoid logging secrets or full proprietary code by default.

---

## 10. Privacy, Safety, and Runtime Boundaries

- **Default offline:** local inference endpoints; external network and frontier calls disabled until explicitly authorized.
- **Workspaces:** repository/worktree path allowlists; sandbox subprocesses where feasible; avoid running untrusted code with broad host privileges.
- **Credentials:** never place secrets in prompt logs, source indexes, or test output. Provide scoped credentials through external secret handling only when indispensable.
- **Repository content is untrusted input:** docs, comments, and retrieved text may contain prompt injection. Such content is evidence, not operational instructions.
- **Authority:** only the control plane grants permissions, merges, triggers privileged execution, or changes approved requirements.
- **Resource limits:** per-worker memory/concurrency/CPU/GPU budgets and bounded work queues. Handle model-server contention gracefully.
- **Recovery:** process/host crash restart tests, stale lease eviction with fencing, reconciliation of in-flight work, and idempotent event replay.
- **Data lifecycle:** allow local export and deletion of project records, and rebuilding of indexes from Git. No vendor lock-in for project history.

---

## 11. Model Selection, Quality Metrics, and Economics

The operator chooses models based on performance **in the role and environment**, not leaderboard score alone. Maintain a capability registry with the operator's assignment decisions, benchmark tasks, context constraints, available RAM/VRAM, median execution time, reviewer pass rate, regression rate, failure categories, and measured local compute costs. Calibration and the PM recommend assignments; only the operator authorizes them. Preserve model versions and prompt templates with results.

**Model assignments are the operator's and already made.** The initial Spark/Bolt assignments have since been changed by the operator; the table records the deployment as of 2026-11-07 (Trailblazer `config/config.exs`). Do not restart model selection as part of this architecture change, and do not treat any of these models as the default owner of other roles. Use the cheapest adequate existing capability for each responsibility:

| Role | Current deployment / model assignment |
|---|---|
| Engineer | `lucet-coder-4b-omega` Q4_K_M (Spark-X2.6-4B code fine-tune; operator pick 2026-11-06, replacing Spark Q8): local coding, whole-file text answers, **thinking off** (see below). |
| Local PM support | `hive-conductor-1.7b` (LFM3-1.7B role fine-tune; replaced Bolt/LFM2.5-24B as planner after a 5/6 vs 0/6 probe): prioritization, task selection, and bounded routing decisions. This does not establish capability for unrestricted decomposition or architectural planning. |
| Analyst | Scout retrieval followed by Ranger analysis with its existing LFM3-1.7B / `lfm3-1.7b:q8` assignment when interpretation is needed. This is not an automatic reviewer assignment. |
| Designer / contract author | Frontier through `HostPlugin` initially; approved contracts can be imported for offline execution. Local authoring requires separate qualification and an operator assignment decision. |
| Verifier | Deterministic locked probes are the primary gate, plus an operator-assigned reviewer model different from the attempt's Engineer (§8.1). Currently deployed: `lfm2.5-24b-a2b:qad-review` (verdict read after its last `</think>`; it caught a half-done change the probes passed, 2026-11-07). The reviewer ADR formally remains open (§16); frontier review requires explicit authorization through `HostPlugin`. |
| Diagnostician | Deterministic failure classification first. Use a model only for escalation; the operator's model assignment remains an open ADR. |
| Integrator | Deterministic Git coordination and regression gates, with no default model. Conflicts requiring code changes create bounded Engineer work and require renewed verification. |

Carry forward the existing screening evidence and preserve the current quantization, draft-model, runtime, sampling, **served chat template and template arguments**, and resource settings in versioned deployment records. The template is part of a model's identity: lucet served with its base model's template was told *"the system is invoked with `thinking_effort=high`"* and answered every retry with that as a tool call until thinking was turned off (2026-11-07); qualify a model with the exact template and arguments it will run with. Trailblazer's `config/config.exs`, `docs/design.md` §7.2, and `docs/local-planner.md` identify the deployed baseline; verify configuration provenance during discovery without starting a new audition for those roles. Validate the new adapters and end-to-end integration against this baseline. Role-specific calibration may support a proposal to widen Spark/Bolt scope; only the operator authorizes that change. Unassigned responsibilities use deterministic tooling where adequate; model-backed work waits for an operator-approved local assignment or explicitly authorized frontier assistance. Do not silently expand an existing model's role or violate local-only operation to fill a gap. A demonstrated capability gap, measured regression, or material deployment change can justify recommending a replacement; the operator decides. Frontier-led planning and final human acceptance retain their existing boundaries.

Measured Spark prompt/edit-harness lessons and the final controlled trial are recorded in [the2026-11-04 field note](docs/notes/2026-11-04-spark-harness-lessons.md); the first unattended night of the dispatch cap and post-mortem rule (0 of 5 packages landed autonomously, 20 harness fixes, failures moving from format to logic) in [the2026-11-07 note](docs/notes/2026-11-07-overnight-dispatch-lessons.md). This evidence does not itself authorize new model assignments or implementation.

Key outcome metrics:

- Correctly accepted requirements per unit of wall time and per GPU-hour;
- First-pass verification rate and regression frequency;
- Median attempts/time per completed work package;
- Percentage of time spent in non-progress retries;
- Each failure's post-mortem class (package too large, under-specified, model, harness) — and whether a harness fix stopped that class recurring. Until harness failures stop dominating, model quality cannot be read from outcomes;
- Rate of missed/stale change notifications caught by reconciliation;
- Unplanned human/frontier escalations and approval latency;
- UAT defects and traceability completeness;
- Improvement benefit after total proposal/probe/verification cost, measured against a matched-budget baseline; transfer regressions, recurrence of targeted failures, and rollback rate;
- Calibration error and overclaim rate per model/role/task class, and whether showing the track record before assessment reduces them over time;
- End-to-end comparison with a single local coding agent on the same tasks. This baseline is captured from the **first** real run, not saved for Day 7: if one local worker plus locked probes in a worktree delivers most of the value, that should reshape priorities early. The baseline does not prescribe a worker runtime for Resonant.

The optimization target is **reliable delivered outcomes**, not token volume, number of agents, or a permanently busy GPU.

---

## 12–16. Delivery plan, acceptance scenarios, layout, agent instructions, open decisions

Moved to [docs/design/delivery-plan-and-open-decisions.md](docs/design/delivery-plan-and-open-decisions.md).

## 17. Core Thesis

**Resonant is a reconciliation-driven software delivery organization, not a conversation-driven agent swarm.**

It manages verified movement from observed software state toward authorized desired software state. Models contribute bounded, specialized judgment and action. Git, durable project records, independent evidence, policy, and actual timers determine what the system can legitimately claim. The BEAM keeps the organization alive and accountable even when its individual workers fail.

The future is the design target. The first week proves the operating model.

---

## Selected Technical References

These are implementation references, not substitutes for checking actual installed versions and behavior:

- Ladybug database and concurrency model: https://docs.ladybugdb.com/concurrency/
- Ladybug database fundamentals: https://docs.ladybugdb.com/
- LadybugEx Elixir binding: https://ladybug-ex.hexdocs.pm/readme.html
- Elixir supervised task source and lifecycle: https://github.com/elixir-lang/elixir/blob/main/lib/elixir/lib/task/supervisor.ex
- git-bug source and integration options: https://github.com/git-bug/git-bug
