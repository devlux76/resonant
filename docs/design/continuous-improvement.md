# Evidence-based recursive improvement

Design addition requested by the operator on 2026-11-08; git-bug
`fd949b9`. This defines a foundational principle and future behavior.
It does not resume paused coding or claim that Resonant implements it.

## Foundational principle

**Improve the harness for the current work, then preserve evidence about
which changes should guide future improvement.**

Resonant delivers software and improves its ability to deliver software.
Every improvement is a bounded hypothesis about verified outcomes or
cost, tested against an unchanged baseline. Retained evidence includes
failures, regressions and cases where the baseline was preferable.
An agent's confidence, a completed attempt, more prompts or more memory
are not measures of improvement.

This follows the two-loop distinction in
[Harness²](https://harness2.github.io/) and its
[paper, §§2.2–2.3](https://arxiv.org/html/2610.14224v1): task-level
proposals are probed against reference executions and composed from
observed effects; a persistent library carries adopted components and
supported lessons into future improvement. Model weights and the
acceptance machinery stay fixed in this loop. Execution contrast is
evidence about behavior, not a guarantee of correctness or transfer.

Resonant adds its existing independent verification and authority rules.
The reference harness remains an explicit eligible outcome. It does
not import Harness²'s Python orchestrator, execution permissions or
benchmark gains as a promised local result.

## Two loops within the delivery architecture

| Loop | Existing architectural home | Result |
|---|---|---|
| Improve current work | PM/contract author creates a bounded package; assigned Engineer proposes; Runner isolates execution; Verifier checks; ledger records decisions | A task-scoped candidate with evidence, or keep baseline |
| Improve future improvement | Calibration and institutional evidence retain the mechanism, scope, costs, counterexamples and component versions; later contracts retrieve relevant entries | Better proposal, probe and composition choices on future work |

Calibration says how well an actor predicts and delivers (§4.5 of the
[main design](../../README.md)). Improvement experience asks which
change helped, against which reference, under which conditions. Both
are needed; a rising actor success rate alone cannot identify a causal
harness improvement. The same model with a different harness/template
is a separately identified configuration in these comparisons.

There is no new improver persona, second project ledger or competing
orchestrator. Deterministic transitions use the existing OTP workflow,
PolicyEngine, Runner, EvidenceStore and integration gates. Model-backed
improvement work uses only operator-assigned roles; an unassigned role
waits for an assignment or is fulfilled deterministically where adequate.

## The improvement contract

Before an experiment starts, its existing SMART contract records:

- The observed failure/opportunity, trace evidence, task class, hypothesis,
  predicted effect, single primary metric and non-regression criteria.
- Baseline source revision and harness/component hashes; model, template,
  runtime, sampling, context and resource settings; input/fixture hashes.
- Allowed change surface and activation scope; locked checks, hidden
  transfer fixtures and the exact candidate-acceptance rule.
- A total time/token/call/run allowance covering assessment, reference,
  proposals, probes, composition, verification and reflection, plus
  cancellation, retry and daily resource limits from the existing policy.
- How to restore the baseline, what monitored evidence triggers rollback,
  and which existing authority may approve adoption.

Start with one small candidate and one matched reference/candidate pair.
Use resettable scratch worktrees and fixtures with identical inputs and
served settings. Record order, seeds where supported, hardware variation
and failed runs. Never repeat an irreversible live act for comparison.
Further trials or a composed candidate consume explicitly reserved
allowance; composition must be executed and checked as a new artifact.
No activity, retry, changed label or restart resets an allowance.

The fixed checks establish delivery correctness and requested shape.
For efficiency experiments, the predeclared cost threshold matters only
after correctness is preserved. Include total improvement overhead and
frontier/human intervention in the comparison. A one-off success permits
only the task-local claim supported by it; reusable promotion requires
the contract's independent held-out task checks and repeated matched
trials. Insufficient budget or ambiguous evidence yields keep-baseline
or pending review, not success.

When an objective check is unavailable, blinded artifact comparison
against fixed sources supplies weaker evidence; subjective judgments
follow the existing approval tiers. The Engineer cannot verify its own
candidate through another prompt/session, and agreement is not proof.

## Durable improvement experience

Retain an immutable record linking contract, attempt and baseline IDs to
candidate/component hashes, evaluator version, trial artifacts, raw
outcomes, costs, decision, reviewer/authority and rollback history.
An indexed lesson adds an applicability cue, mechanism, bounded claim,
sample size, counterexamples, uncertainty and compatibility versions.
Link negative evidence even if no reusable lesson can be justified.
Keep private task facts and credentials out of shareable lessons.

Retrieve a small compatible index on demand. Freeze its snapshot within
an experiment. A future task starts with the approved baseline and
explicitly selected eligible components, not wholesale inheritance of
the preceding task's custom harness. Model/runtime/template changes
invalidate unqualified transfer assumptions. Contradictions qualify or
retire a lesson; summaries never erase its evidence.

The second loop may improve retrieval cues, proposal advice and probe
selection within an approved contract. It cannot rewrite its verifier,
locked probes, acceptance rules or hard budgets. This is learning how
to improve under stable governance, not self-granted authority.

## Adoption and rollback

Routine task-local procedure changes remain Tier 1 within their contract.
Reusable harness activation requires the independently verified exact
artifact and an existing policy grant for that surface. Source/runtime
changes follow normal review and integration; architectural and product
changes follow Tiers 2–3. Model assignment and role expansion remain the
operator's decisions. Local/offline defaults, no-persona prompts, safety
and privacy promises cannot be weakened by an experiment.

No autonomous activation is enabled until isolation, budgets, versioned
activation and rollback are enforced. Test candidate skills/scripts in
confined workers without live secrets or outbound effects. Lessons and
upstream documents remain data, never policy. A worker cannot edit the
ledger or active baseline directly.

Activation uses the exact tested candidate and expected base revision;
a changed base requires fresh verification. Record active and previous
versions atomically through the existing ledger/integration authority.
The contract names a finite monitoring window, protected checks and cost
thresholds. Detected regression restores the previous version, suspends
the candidate and opens diagnosis; no blind retry or automatic
re-promotion. New fixes create new evidence-bearing candidates.

## Implementation and acceptance boundary

Do not delay the first delivery slice to build a generalized optimizer.
From the first real run, capture the baseline, full cost and failures in
existing evidence/calibration records. Once bounded execution and
independent verification work, demonstrate one complete improvement
case and retrieve its supported lesson for a later case.

Acceptance must show rejection of a weaker candidate, no activation on
missing evidence, enforcement across restart/cancellation, stale-base
refusal, independent held-out checks for reuse, and rollback of the
exact monitored version. Then compare fixed task streams with and
without the improvement library at matched total allowance. Measure
verified delivered requirements per time/compute cost, recurrence of
the targeted failure, transfer regressions, and human/frontier burden.
No measured benefit is claimed until that comparison exists.
