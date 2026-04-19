# Better Harness Reference

Use this folder when a project needs harness hill-climbing: improving the agent wrapper around a model with evals, rather than treating "agent quality" as a single prompt or model-selection problem.

## Sources studied

- LangChain Deep Agents example: https://github.com/langchain-ai/deepagents/tree/main/examples/better-harness
- Article: https://reads.little-shock.top/articles/better-harness-a-recipe-for-harness-hill-climbing-with-evals/

## What the sources say

The `better-harness` example is a research artifact for letting one outer agent improve another inner agent's harness with evals. The target is not the base model. The target is the harness around it: prompts, tools, skills, middleware, registration code, routing, and other loaded surfaces.

The article adds an important framing shift: evals are not only a release checklist. Inside a bounded loop, they are the learning signal that tells the system which harness behaviors deserve to persist.

The core loop is:

1. Run a baseline.
2. Materialize a proposer workspace for the outer agent.
3. Let the outer agent edit only the allowed harness surfaces.
4. Evaluate the edited inner agent on train and holdout cases.
5. Keep the candidate only if the combined pass count improves.
6. Optionally run a broader scorecard on baseline and final only.

The article frames this as a practical recipe for hill-climbing with evals. The important move is to combine a fixed eval harness with a narrow editable surface, so an agent can propose concrete harness changes and the system can keep or discard them based on measured behavior.

## What to absorb into Agent Eval Flywheel

1. Treat the harness as the unit of improvement.
   - Do not reduce the loop to prompt tuning.
   - Include prompts, tool definitions, skills, middleware, routing policy, retrieval config, escalation policy, and the code that wires those pieces together.

2. Require a surface manifest.
   - List exactly what may change.
   - Each surface should map to a real file, config value, module attribute, or UI/policy artifact that the target system actually loads.
   - Do not let the optimizer edit notes, TODOs, or files that are not connected to runtime behavior.

3. Split cases by role.
   - Optimization set: visible failures for diagnosis and iterative edits.
   - Holdout set: private or less-visible cases for keep/discard decisions.
   - Scorecard: broader acceptance slice used at baseline/final or major milestones, not every small iteration.

4. Tag by stratum.
   - Use behavior strata such as `tool_use`, `retrieval`, `planning`, `conversation`, `policy`, `memory`, `latency`, or `cost`.
   - Keep optimization and holdout sets matched by stratum, otherwise a candidate can appear better by solving only an easy slice.

5. Persist local artifacts.
   - Store failures, traces, run summaries, candidate diffs, and accept/reject decisions.
   - Treat local artifacts as the source of truth for later failure analysis.

6. Guard against visible-case overfitting.
   - Visible failures are diagnostic clues, not the target itself.
   - Before editing the harness, infer the broader policy or behavior that explains the failures.

7. Manage case lifecycle deliberately.
   - Do not only append new cases forever.
   - Separate cases that are still optimization-sensitive from cases that are now regression-critical or smoke-like.
   - Retire or downgrade stale optimization cases carefully, but preserve hard-won regression and contract coverage.

## What not to copy literally

- Do not require Deep Agents, LangChain, pytest, Harbor, LangSmith, or a specific runner.
- Do not treat visible/private splits as a security sandbox.
- Do not use pass count as the only metric for product systems with safety, trust, cost, or UX tradeoffs.
- Do not allow an autonomous optimizer to expand permissions, change rubric weights, or redefine the task without a human checkpoint.
- Do not assume scorecard cases should run every iteration. They are often expensive and can become another overfitting target.

## How this maps to the existing flywheel

| Better-harness concept | Agent Eval Flywheel mapping |
| --- | --- |
| Inner agent | Target system |
| Outer agent | Human or agent proposer inside a bounded loop |
| Editable surfaces | Mutable surface for the current target-system loop |
| Train cases | Optimization cases used for diagnosis |
| Holdout cases | Keep/discard protection against overfitting |
| Scorecard | Broader milestone or release benchmark |
| Stratum | Behavior bucket used to balance coverage |
| Proposer workspace | Candidate patch workspace or branch |
| Decision log | Run ledger |
| Traces | Failure evidence for regressions and eval-loop updates |

## When to use this reference

Open `harness-hill-climbing.md` when:

- a user asks how to improve an agent iteratively with evals
- a project already has evals but no clear train/holdout/scorecard topology
- the agent has multiple improvement surfaces beyond prompts
- benchmark gains are suspicious because visible cases may be overfit
- traces exist but are not being converted into cases and regressions
