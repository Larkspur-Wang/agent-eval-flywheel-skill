# Autoresearch Principles For Agent Evals

This note maps `karpathy/autoresearch` to eval-driven agent development.

## The useful transfer

`autoresearch` is not mainly about training models overnight. The transferable idea is to encode the research loop itself so the agent can keep improving within a fixed envelope.

Map the pieces like this:

- fixed evaluation harness in `prepare.py` -> fixed eval bundle, graders, and budgets
- mutable `train.py` -> prompts, policies, retrieval, tool routing, code, or UX
- `program.md` -> the skill or workflow instructions that define how the loop runs
- `results.tsv` -> the project run ledger

## Principles to carry over

1. Freeze the comparison surface.
   - Decide what is fixed for the current run.
   - Do not change the grader and the judged system in the same comparison.
2. Baseline first.
   - The first run exists to measure the current system, not to celebrate progress.
3. Keep one ledger.
   - Record baseline, winning runs, tradeoffs, and crashes in one place.
4. Prefer the smallest coherent mutation.
   - Change one surface or one tightly related bundle at a time.
5. Keep only wins.
   - Advance on strict improvement, simplification, or explicit human-approved tradeoff.
6. Reversion is a feature.
   - If the change does not survive the fixed eval surface, revert.
7. Keep loops short.
   - Cheaper loops create more learning opportunities and cleaner attribution.
8. Treat workflow text as code.
   - The instructions that define the loop deserve iteration too.

## What not to copy literally

Do not copy these parts without adjustment:

- "never stop" autonomy
- single-metric optimization
- zero human checkpoints

Product and agent systems usually need humans for:

- goal changes
- permission expansion
- business tradeoffs
- rubric reweighting
- production rollout

## Recommended product-safe adaptation

Use autonomous iteration inside a bounded loop, but add human triggers at:

- task definition
- acceptance criteria
- risky tradeoffs
- eval-version changes
- production promotion

This keeps the speed benefits of `autoresearch` without turning the benchmark into an ungoverned optimizer.

## Common failure modes

- no fixed harness, so every run claims progress
- no regression capture, so the same failures escape again
- no distinction between hard gates and soft scores
- no versioning for graders or eval cases
- no correlation work between benchmark gains and business gains

## Short rule

Use `autoresearch` as a mindset for disciplined iteration, not as a permission slip for unchecked autonomy.
