# Harness Hill-Climbing Pattern

Use this pattern to adapt the `better-harness` idea to any agent stack.

## Core principle

Optimize the harness, not the anecdote. A harness is the set of prompts, tools, skills, policies, retrieval settings, middleware, routing, UI copy, memory behavior, and wiring that shapes how the target agent behaves.

## Recipe

1. Define the job and fixed runner.
   - Lock the task, user, environment, allowed tools, hard gates, rubric, and eval runner.
   - Run a baseline before changing any surface.

2. Create a surface manifest.
   - Name each editable surface.
   - Map it to the actual runtime artifact.
   - State why it is allowed to change this loop.
   - State what must stay fixed.

3. Split cases by role.
   - Optimization cases are used for diagnosing failures.
   - Holdout cases are used for keep/discard decisions.
   - Scorecard cases are used for baseline/final or milestone checks.

4. Balance by stratum.
   - Add a `stratum` field to every case.
   - Keep optimization and holdout sets matched by stratum.
   - If a stratum has no holdout case, do not claim robust improvement for that stratum.

5. Propose one candidate.
   - Edit only allowed surfaces.
   - Make the smallest coherent change.
   - For code surfaces, edit real code and real wiring, not explanatory notes.
   - For policy or prompt surfaces, encode the general rule behind visible failures.

6. Evaluate candidate.
   - Run the same optimization and holdout cases.
   - Compare against the previous accepted candidate, not against a moving target.
   - Record cost, latency, crashes, and hard-gate failures, not only pass count.

7. Decide.
   - Keep strict improvement.
   - Keep neutral score only if the harness is simpler or safer.
   - Mark tradeoffs for human review.
   - Discard candidates that only improve visible cases while hurting holdout.

8. Save artifacts.
   - Store candidate diff, result summary, failure list, trace references, grader outputs, and the accept/reject reason.
   - Convert escaped failures into regression cases in the eval-bundle loop.

9. Review the case portfolio.
   - Keep optimization-sensitive cases small and sharp enough to expose change.
   - Preserve regression-critical and smoke cases even after they saturate.
   - Retire stale optimization cases only when they stop teaching you something.

## Surface manifest template

```yaml
version: "v0"
fixed_this_loop:
  - "rubric weights"
  - "eval runner"
  - "case set"
  - "latency budget"
editable_surfaces:
  - name: "system_prompt"
    type: "prompt"
    runtime_target: "agent/prompts/system.md"
    why_editable: "Primary policy and behavior guidance."
    guardrails:
      - "Do not weaken safety or permission checks."
  - name: "tool_routing"
    type: "policy"
    runtime_target: "agent/routing/tool_policy.yaml"
    why_editable: "Tool selection failures dominate current baseline."
    guardrails:
      - "Do not add new tools in this loop."
  - name: "retrieval_config"
    type: "config"
    runtime_target: "agent/retrieval.yaml"
    why_editable: "Retrieval misses cluster in the support-policy stratum."
    guardrails:
      - "Keep approved data sources fixed."
```

## Case topology template

```yaml
cases:
  - id: "tool-use-optimization-001"
    split: "optimization"
    stratum: "tool_use"
    source: "production-trace"
    trace_refs:
      - "runs/2026-04-19/baseline/tool-use-001.json"
    user_task: "Ask the agent to create and send a report."
    must_do:
      - "Choose the reporting tool."
      - "Ask only the minimum missing follow-up question."
    must_not:
      - "Ask for data already present in context."

  - id: "tool-use-holdout-001"
    split: "holdout"
    stratum: "tool_use"
    source: "pm-example"
    user_task: "Ask the agent to send a related but not identical report."
    must_do:
      - "Choose the reporting tool when destination and core intent are clear."
    must_not:
      - "Overfit to the wording of the optimization case."
```

## Decision record template

```yaml
iteration: 3
candidate_id: "candidate-003"
changed_surfaces:
  - "system_prompt"
  - "tool_routing"
baseline_ref: "run-002"
optimization:
  hard_pass: true
  score: 0.78
holdout:
  hard_pass: true
  score: 0.72
decision: "keep"
reason: "Improved tool-use and conversation strata without holdout regression."
tradeoffs:
  - "Latency increased by 0.4s but remains under budget."
next_regressions:
  - "Add escaped retrieval miss from trace abc123."
```

## Case lifecycle buckets

Use one of these buckets when maintaining a mature eval bundle:

- `optimization_sensitive`
  - Small, information-dense cases that still discriminate between candidate variants.
- `regression_critical`
  - Cases that encode real escaped failures and must stay green even if they are no longer score-sensitive.
- `smoke`
  - Lightweight contract checks that protect the basic behavior floor.
- `historical`
  - Older cases kept for comparability or audit, but not necessarily run every small loop.

This is safer than deleting "easy" cases wholesale.

## Anti-patterns

- Editing the target system and the eval cases in the same comparison window.
- Exposing an editable surface that the runtime never loads.
- Running only optimization cases and calling the result production-ready.
- Letting scorecard cases become the visible training target.
- Improving aggregate pass count while losing a safety hard gate.
- Treating trace links as enough evidence without saving the relevant local artifacts.
- Asking the optimizer to "make it better" without a surface manifest.

## Short rule

For each loop, make the mutable surface explicit, keep the eval topology fixed, and require a written keep/discard reason backed by artifacts.
