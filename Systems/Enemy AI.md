### Purpose
- Determines how an enemy selects its next intent
- Produces a valid [[Enemy Intent]] based on defined rules

## Core Rule
- Enemy intent selection must be deterministic or explicitly defined randomness

## Data

pattern
- rule set defining possible intents

state
- internal state used by the pattern (optional)

rng
- random source if behavior is non-deterministic

## Rules

- Each enemy must define how it selects next_intent
- Selection occurs after current intent is executed
- Selection does not modify game state
- Selection only produces data (next_intent)

## Pattern Types

fixed_sequence
- intents follow a predefined ordered list

Example:
- turn 1: attack
- turn 2: buff
- turn 3: attack

cycle
- repeats a fixed sequence

random_weighted
- selects from a set of intents using weights

conditional
- selects intent based on game state

Examples:
- if player HP < 50% → attack
- else → buff

## Order of Operations

1. Enemy finishes executing current intent
2. Evaluate pattern
3. Select next_intent
4. Store as next_intent on enemy

## Selection Rules

- Selection must produce a fully defined [[Enemy Intent]]
- Selection cannot fail
- If multiple valid options exist:
  - resolve via pattern rules (order, weights, conditions)

## Interaction

- Used by [[Enemy System]] after intent execution
- Provides data for next [[Enemy Phase]]

## Edge Cases

No valid pattern output:
- must fallback to a default intent

Random selection:
- must use consistent RNG if reproducibility is required

State-based patterns:
- must not depend on undefined state

## Standardization Rules

- AI does not execute actions
- AI only selects intent
- All execution happens through [[Enemy Intent]] → [[Action]]

## Related
- [[Enemy System]]
- [[Enemy Intent]]
- [[Combat Controller]]