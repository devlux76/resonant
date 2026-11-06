# Note: frontier AI spend is being capped (operator, 2026-11-06)

Status: context for why Resonant exists. Not yet folded into README.md; it
belongs with the design's motivation.

## The report

MacRumors, 2026-11-04, citing Bloomberg:
<https://www.macrumors.com/2026/11/04/apple-reducing-employee-ai-spending/>

- Apple is cutting employee AI spending to reduce costs. In October some
  engineers' daily allowance dropped from $300 to $200, and some may drop to
  $100. Many non-engineering groups are already at $100 a day, and some teams
  could be capped at $25.
- Engineers have the largest budgets because AI-assisted coding uses many
  tokens.
- Some teams are being asked to use cheaper models or **switch to on-device
  AI**. A larger budget needs an executive's approval.
- Employees can use Claude, ChatGPT, Gemini and others. Some said few people
  used the full $300, so the savings may be small; the direction is the point.

## Why it matters for Resonant

Coding with frontier models is metered, and even a company with Apple's
resources is now rationing it per engineer per day and steering work to
cheaper and on-device models. That is the situation Resonant is built for:

- **Local models do the routine work.** Planning, coding, review and
  verification run on local models on the operator's own hardware, at no
  per-token cost; frontier models are reserved for judgment (contracts,
  decisions, cross-review), where they are worth the price.
- **Frontier spend is a budget, not a default.** A per-day allowance is a
  constraint the system should plan against: escalate to a frontier model only
  when local attempts fail or the decision warrants it, and record what each
  escalation cost and bought.
- **Getting more from small models compounds.** The work on edit formats a 4B
  model can actually produce, worked examples from our own history (the
  replay memory), and weekly fine-tuning (note of 2026-11-03) each move work
  from the metered tier to the free one.
