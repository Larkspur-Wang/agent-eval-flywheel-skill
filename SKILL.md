---
name: agent-eval-flywheel
description: "Design and run eval-driven development for AI agents and AI-powered products. Use when Codex needs to turn a job into goals, boundaries, hard acceptance gates, soft rubrics, seed eval sets, regression suites, virtual-user scenarios, or a benchmark-driven double loop where the target system and the eval bundle co-evolve."
---

# Agent Eval Flywheel

Use this skill to attach a new or existing agent to a small, versioned eval bundle before large implementation. The desired output is a stable improvement loop, not a one-off benchmark deck.

## Core stance

- Define the job before the benchmark.
- Separate hard gates from soft scores.
- Freeze the comparison surface for the current loop.
- Run a baseline before changing the system.
- Expose only real editable harness surfaces that the target system actually loads.
- Keep optimization, holdout, and optional scorecard cases separated when hill-climbing.
- Keep only improvements that survive the fixed eval surface or are explicitly accepted as a human tradeoff.
- Grow regressions from real failures.
- Evolve the eval bundle in a separate loop from the target-system loop.

## Quick start

1. Lock the unit of evaluation.
   - Write one concrete job: user, task, environment, tools, expected artifact.
   - Avoid vague goals such as "make the agent better."
2. Freeze the current loop.
   - Decide what may change this round: prompt, retrieval, tool routing, policy, code, UX, or architecture.
   - Decide what must stay fixed: evaluator, rubric weights, seed cases, latency budget, cost budget, or allowed tools.
   - List the real editable harness surfaces: prompt files, skill files, tool definitions, middleware, wiring, routing policy, retrieval config, UI copy, or escalation policy.
3. Split hard gates from soft scores.
   - Hard gates block shipping: safety, permissions, required workflow, schema validity, or critical factual grounding.
   - Soft scores rank good behavior: task success, clarity, preference, latency, cost, or elegance.
4. Build the seed eval bundle.
   - Start with 10-30 cases from real tasks, prior failures, edge cases, and adversarial cases.
   - Include both likely-pass and likely-fail cases.
   - For hill-climbing, split cases into optimization, holdout, and optional scorecard sets, and keep optimization and holdout matched by stratum.
5. Run the baseline.
   - Measure current performance before making changes.
   - Save the result ledger.
6. Iterate in the target-system loop.
   - Make the smallest coherent change likely to move the score.
   - Re-run the same eval surface.
   - Keep, revert, or explicitly accept a tradeoff.
7. Iterate in the eval loop.
   - Add new regressions from production failures.
   - Calibrate graders against human judgments.
   - Version the eval bundle and rebaseline.

## Required outputs

Produce these artifacts unless the user asks for a lighter pass:

- an agent charter with objective, user, task, context, allowed tools, and forbidden actions
- a rubric with hard gates and weighted soft scores
- a seed eval set
- a regression set
- an error taxonomy
- a run ledger for baselines and iterations

Use `scripts/init_eval_bundle.py` to scaffold these files when starting from scratch.

## Double-loop workflow

### Loop A: target system

1. Run the fixed eval bundle and capture the baseline.
2. Cluster visible optimization failures by stratum and error type, not by anecdote.
3. Pick one real editable surface, or one tightly coupled surface bundle.
4. Implement the smallest coherent change on files, prompts, tools, policies, or wiring that the target system actually loads.
5. Re-run the same optimization and holdout surfaces.
6. Use the optimization set for diagnosis, the holdout set for keep/discard, and the scorecard only for baseline/final or milestone checks.
7. Keep only one of:
   - strict improvement
   - neutral score with a simpler system
   - deliberate tradeoff approved by the human

### Loop B: eval bundle

1. Collect missed failures, user complaints, and surprising successes.
2. Distill them into new cases, personas, or scenarios.
3. Calibrate judges against human review on a sample.
4. Version the bundle instead of silently editing it.
5. Rebaseline before claiming improvement against the new version.

Never claim that the system improved if both the target system and the eval bundle changed in the same comparison window.

## Autoresearch mindset

Borrow the following ideas from `karpathy/autoresearch` without copying its exact operating mode:

- Treat the workflow instructions as research-org code.
- Keep a narrow mutable surface and a clearly fixed harness for each loop.
- Establish a baseline first.
- Record every run in one ledger.
- Advance only on improvement, acceptable simplification, or an explicit human-approved tradeoff.
- Revert or discard losing variants.
- Keep loops short enough that iteration remains cheap.
- Let the system run autonomously inside a well-scoped envelope, but keep humans as the trigger for goal changes, permission expansion, and metric conflicts.

## Better-harness mindset

Borrow the following ideas from LangChain's `better-harness` example without tying this skill to Deep Agents:

- Treat the agent wrapper as an editable harness, not just a prompt.
- Give the optimizer a narrow set of real loaded surfaces and a fixed eval runner.
- Keep training-like optimization cases separate from holdout cases.
- Match optimization and holdout by behavior stratum so gains are not concentrated in one easy slice.
- Keep a larger scorecard for baseline/final comparisons instead of using it for every small loop.
- Save local run artifacts, failures, traces, and decisions as the source of truth.
- Warn against overfitting visible failures: infer the general policy behind a failure before editing the harness.

For detailed adaptation notes, open `references/better-harness/README.md`.

## Human triggers

Pause and align with the user before:

- expanding permissions, tools, or data access
- changing business priority or rubric weights
- accepting regressions in safety, compliance, or trust
- redefining the task itself
- editing graders or eval cases while using them to justify a product win
- moving a change into production

## Case selection rules

- Prefer real user tasks over synthetic-only prompts.
- Sample across happy path, boundary path, and adversarial path.
- Cover both capability and behavior.
- Tag cases with a behavior stratum such as `tool_use`, `retrieval`, `planning`, `conversation`, `policy`, or `memory`.
- When using train-like optimization and holdout splits, keep the same strata represented in both.
- Keep cases self-contained and reproducible.
- Store the expected constraints, not only the expected answer.
- Add every escaped production bug to the regression set.

## Judge guidance

- Prefer deterministic checks first: schema validity, tool sequence, retrieved citation presence, latency budgets, or cost budgets.
- Use model judges for preference, tone, helpfulness, and nuanced rubric dimensions.
- Sample human review regularly to calibrate automated judges.
- Keep judge prompts and rubric text versioned with the eval bundle.
- Persist traces, failure artifacts, grader outputs, and candidate decisions with the run ledger whenever possible.

## User simulator guidance

Add virtual users when product quality depends on persona, scenario, or longitudinal context.

- Start with a small persona grid, not a giant synthetic population.
- Vary only a few scenario axes per batch.
- Keep persona generation separate from scoring.
- Treat synthetic users as a high-frequency diagnostic surface, not as final truth.

For detailed patterns, open `references/user-simulator-patterns.md`.

## Resources

Open only what is needed:

- `references/eval-bundle-schema.md` for file shapes, field definitions, and case examples
- `references/user-simulator-patterns.md` for persona and scenario design
- `references/autoresearch-principles.md` for the research-loop mapping
- `references/better-harness/README.md` for harness hill-climbing, editable surfaces, train/holdout/scorecard splits, and trace-backed iteration
- `scripts/init_eval_bundle.py` to scaffold a new eval bundle

## Deliverable pattern

When asked to set up the loop for a project, usually produce:

1. the agent charter
2. the rubric
3. the first seed eval set
4. the regression capture path
5. the next-iteration plan

If the user already has an agent, begin with failure collection and baseline scoring instead of rewriting the charter from scratch.
