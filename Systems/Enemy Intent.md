### Purpose
- Defines the action an enemy will perform on its turn
- Produces [[Action]]s when executed

## Core Rule
- An intent is data that describes future actions; it does not execute until the enemy acts

## Data

type
- behavior category

value
- numeric magnitude (damage, block, etc.)

targeting
- targeting type used to resolve target(s)

effects
- list of effect definitions used to generate [[Action]]s

## Types

attack
- deals damage

block
- gains block

buff
- applies positive status

debuff
- applies negative status

mixed
- combination of multiple effects

## Rules

- Each enemy has exactly one current intent
- Intent must fully define what happens when executed
- Intent does not modify game state until execution
- Intent must be valid before execution

## Order of Operations

Intent lifecycle:

1. Intent is determined and stored as next_intent
2. At start of enemy turn:
   - next_intent becomes intent
3. During [[Enemy Phase]]:
   - intent is executed
4. After execution:
   - next_intent is determined again

## Execution

1. Resolve targeting using [[Targeting]]
2. Generate effects from intent data
3. Generate [[Action]]s using resolved target(s)
4. Enqueue actions into [[Action Queue]]

## Targeting

- Uses [[Targeting]] system
- Targeting must be resolved before action generation
- Common cases:
  - player
  - all_enemies
  - all_entities

## Interaction

- Executed during [[Enemy Phase]]
- Generates [[Action]]s
- Modified by [[Status Effects]] during execution

## Edge Cases

Invalid target at execution:
- action fizzles

Enemy dies before execution:
- intent is not executed

Multiple effects:
- executed in defined order

## Standardization Rules

- Intent must be fully defined before execution
- Execution always produces actions, never direct state changes
- Targeting is resolved once per execution

## Related
- [[Enemy System]]
- [[Enemy Phase]]
- [[Targeting]]
- [[Action]]
- [[Action Queue]]