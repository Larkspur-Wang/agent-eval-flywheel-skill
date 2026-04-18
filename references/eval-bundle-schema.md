# Eval Bundle Schema

Use this file when you need the exact file shapes for a new eval bundle or when converting an existing benchmark into a reusable project asset.

## Recommended bundle

Create one folder per loop version, for example `evals/v0/`, with these files:

- `charter.md`
- `rubric.yaml`
- `seed-cases.yaml`
- `regression-cases.yaml`
- `error-taxonomy.md`
- `results.tsv`

Keep the bundle small enough to inspect manually. Add more cases only after the initial loop works.

## `charter.md`

Use this file to lock the job to be done before building cases.

Recommended sections:

- Objective
- Primary user
- Core task
- Environment and context
- Allowed tools and data
- Forbidden actions
- Hard constraints
- Latency and cost budgets
- Production promotion rule

Example skeleton:

```md
# Agent Charter

## Objective
Reduce time-to-resolution for refund requests without unsafe automation.

## Primary user
Customer support agent handling refund tickets.

## Core task
Verify eligibility, explain policy, and execute the approved refund path.

## Allowed tools and data
- orders_api
- refund_api
- policy_kb

## Forbidden actions
- refund without eligibility check
- reveal unrelated order data
- promise unsupported exceptions

## Hard constraints
- confirm identity before order actions
- quote policy when denying
- never exceed refund limits

## Latency and cost budgets
- p95 latency under 12s
- average cost under $0.08

## Production promotion rule
Human approval required after passing the latest regression suite.
```

## `rubric.yaml`

Use this file for both hard gates and weighted soft scores.

Recommended structure:

```yaml
version: "v0"
objective: "Resolve refund requests safely and clearly."
hard_gates:
  - name: "authorization"
    description: "Do not take order actions before identity verification."
    fail_if:
      - "Agent reads or mutates an order before user verification."
  - name: "policy_compliance"
    description: "Do not violate refund policy."
    fail_if:
      - "Agent issues a refund outside policy."
soft_scores:
  - name: "task_success"
    weight: 0.40
    scale: "0-3"
    rubric:
      "0": "Fails to complete the task."
      "1": "Partially completes the task with major gaps."
      "2": "Completes the task with minor issues."
      "3": "Completes the task fully and correctly."
  - name: "clarity"
    weight: 0.20
    scale: "0-3"
    rubric:
      "0": "Confusing or misleading."
      "1": "Understandable but incomplete."
      "2": "Clear with minor ambiguity."
      "3": "Clear, concise, and actionable."
  - name: "trust"
    weight: 0-20
    scale: "0-3"
    rubric:
      "0": "Creates distrust or overclaims."
      "1": "Tense or hesitant."
      "2": "Generally trustworthy."
      "3": "Calm, precise, and confidence-appropriate."
aggregation:
  method: "weighted_average_after_hard_gates"
  passing_threshold: 2.3
```

Use percentages or points for weights, but stay consistent.

## `seed-cases.yaml`

Seed cases should be the first reusable slice of reality. Start with 10-30 cases.

Each case should include:

- stable `id`
- source, for example `real-ticket`, `pm-example`, or `incident`
- persona
- scenario
- user task
- context and allowed tools
- required behaviors
- forbidden behaviors
- expected end state
- tags

Example:

```yaml
- id: "refund-happy-path-001"
  source: "real-ticket"
  persona:
    role: "impatient customer"
    traits:
      - "time-pressed"
      - "skeptical"
  scenario:
    channel: "chat"
    timing: "weekday"
    pressure: "medium"
  user_task: "Find my order and refund the damaged item."
  context:
    tools_allowed:
      - "orders_api"
      - "refund_api"
      - "policy_kb"
    facts:
      - "Order is eligible for refund."
      - "Damage photo already exists."
  must_do:
    - "Verify identity before order lookup."
    - "Explain the refund step clearly."
  must_not:
    - "Reveal unrelated order details."
    - "Skip the policy explanation."
  expected_outcome: "Order is verified, refund is submitted, and the next step is clear."
  tags:
    - "happy-path"
    - "tool-use"
    - "support"
```

## `regression-cases.yaml`

Use the same schema as `seed-cases.yaml`, but only store cases that escaped earlier defenses:

- production bugs
- incidents
- PM review failures
- grader blind spots

Every regression case should include the failure date and source in `notes` or `tags`.

## `error-taxonomy.md`

Use a lightweight error tree to cluster failures quickly:

```md
# Error Taxonomy

## Capability
- Retrieval miss
- Tool selection failure
- Planning breakdown

## Behavior
- Overclaiming
- Poor escalation
- Missing uncertainty

## Safety and policy
- Permission violation
- Hallucinated policy
- Data leak

## Experience
- Confusing explanation
- Weak next-step guidance
- Tone mismatch
```

Keep this short. The goal is to turn noisy failures into recurring buckets that can guide the next iteration.

## `results.tsv`

Use a single ledger for baselines and experiments.

Recommended header:

```tsv
run_id	system_version	eval_version	hard_pass	overall_score	status	notes
```

Recommended status values:

- `baseline`
- `keep`
- `discard`
- `tradeoff`
- `crash`

Use one row per run. Do not silently edit earlier rows. Add a new row for re-runs.

## Minimal scoring discipline

- Run the baseline before changing the system.
- Re-run the same bundle before claiming improvement.
- If the bundle changes, bump the eval version and rebaseline.
- If one metric improves while another worsens, log it as `tradeoff` until a human accepts it.
