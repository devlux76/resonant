# Suggested Repository Layout (design §14)

Moved out of the design README unchanged (2026-11-07) so the README stays within the 500-line rule's ratchet.

Use the repository structure best suited to the actual codebase and Elixir version, but maintain these conceptual boundaries:

```text
resonant/
  apps/ or lib/resonant/
    core/             # domain entities, contracts, state machine, policies
    delivery/         # charter, WBS, PM exceptions, scheduling, risk
    execution/        # leases, worker supervision, timeouts, worktrees
    evidence/         # Git reconciliation, test receipts, event ingestion
    context/          # indexing and retrieval ports
    models/           # registry, prompts, worker adapters
    integration/      # review, merge, release/UAT export
    interface/        # CLI and optional LiveView Kanban
  priv/               # migrations, versioned schemas
  test/               # domain, contracts, integration, failure injection
  docs/
    architecture.md
    decisions/
    operations.md
    handoffs/
  examples/
    project_spec.md
    task_contract.yaml
    uat_packet.md
```

Do not force an umbrella app for a small codebase, but do not collapse modules into a single enormous agent orchestration module.
