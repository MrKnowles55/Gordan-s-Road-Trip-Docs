
## Definition
- the rules that determine whether a card or effect requires a target, what targets are valid, and how targets are selected.

## Purpose
- Prevent illegal plays
- Ensure card effects are applied to the correct entities
- Standardize target validation across cards and actions

## Targeting Types

none
- No target is selected
- Example: gain block

self
- Target is the source entity
- Example: gain strength

single_enemy
- Exactly one living enemy must be selected

all_enemies
- all living enemies are targeted
- No manual selection required

all
- All valid entities are targeted
- No manual selection required

## Targeting Rules

- Targeting is defined by the card or effect
- A card cannot be played unless its targeting rule is satisfied
- single_enemy requires exactly one valid living enemy
- none, all_enemies, and do <b>not</b> require manual target selection
- self always resolves to the source entity

## Valid Target

A target is valid if:

- it exists
- it is in play
- it matches the targeting rule
- it is alive if the effect requires a living target

## Validation Timing