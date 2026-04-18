# User Simulator Patterns

Use this file when product quality depends on persona, scenario, or longitudinal context and a static prompt set is not enough.

## When to add virtual users

Add virtual users when the product question is closer to:

- "Would this kind of user trust the system?"
- "Would this workflow still work under pressure?"
- "Does behavior change across roles, moods, or constraints?"

Do not start with a giant synthetic population. Start with a small matrix that makes failure modes legible.

## Separate persona from scenario

Keep these independent:

- Persona: who the user is
- Scenario: what situation they are in
- Rubric: how success is judged

If you entangle all three in one prompt, you will not know what actually changed the score.

## Persona axes

Pick 2-4 persona axes that matter for the product:

- role or expertise
- patience level
- trust sensitivity
- goal clarity
- risk tolerance
- domain history

Example small grid:

- novice user
- expert user
- anxious user
- skeptical user

## Scenario axes

Vary only a few scenario axes per batch:

- timing: normal, urgent, after-hours
- channel: chat, email, GUI workflow
- information quality: complete, partial, contradictory
- environment: calm, interrupted, low bandwidth

This keeps comparisons interpretable.

## Build cases from real traces first

Prefer these sources, in order:

1. real user sessions or tickets
2. PM or support examples
3. synthesized variations of real cases
4. public-data-based personas

Use synthetic users to expand coverage, not to replace real failure signals.

## Judge synthetic users carefully

Virtual users are best for:

- preference signal
- diagnostic signal
- repeated scenario coverage

They are weaker for:

- true business impact
- actual retention or conversion
- long-horizon trust without calibration

Use human review or production telemetry to calibrate synthetic-user outputs on a sample.

## Small-batch design

Good starting batch:

- 4 personas
- 3 scenarios
- 5-10 core tasks

This is enough to expose interaction effects without creating unmanageable output.

## Longitudinal cases

For memory-heavy or health-like agents, add repeated interactions:

- first session
- follow-up session
- correction session
- escalation session

Judge both the single turn and the cross-session consistency.

## Anti-patterns

- generating thousands of synthetic users before defining the rubric
- changing persona wording and rubric wording in the same experiment
- using synthetic-user scores as direct business truth
- hiding failed personas instead of turning them into regressions

## Recommended rule

Use virtual users to make product-level evaluation cheap and frequent. Use real user behavior to keep the simulator honest.
