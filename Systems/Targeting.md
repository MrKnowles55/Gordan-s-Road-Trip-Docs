## TARGETING

### Purpose
- Defines how targets are selected for cards and enemy intents
- Produces target data used for [[Action]] generation
- Ensures targeting is explicit and consistent

## Core Rule
- Targeting defines WHO is affected, not relative relationships
- Targeting does not depend on source (player vs enemy)
- Targeting is resolved before actions are created

## Targeting Types

none
- No target required

self
- Source is the target

player
- Target is the player

single_enemy
- Requires exactly one selected living enemy

all_enemies
- Targets all living enemies

all_entities
- Targets player and all living enemies

## Target Resolution

Targeting produces one of:

- target (single entity)
- targets (multiple entities)
- no target

This data is used to generate [[Action]]s.

## Order of Operations

1. Card play or enemy intent begins
2. Targeting requirements are checked
3. Required target is selected (if applicable)
4. Target or targets are resolved
5. Effects generate [[Action]]s using resolved target data
6. Actions are enqueued into [[Action Queue]]

## Validation

- single_enemy requires exactly one living enemy
- all_enemies requires at least one living enemy (recommended)
- all_entities requires at least one valid entity (recommended)
- player requires player exists
- self requires source exists

If validation fails:
- card cannot be played / intent cannot resolve

## Action Interaction

- Targeting resolves before [[Action]] creation
- [[Action]]s receive resolved target or targets
- [[Action]]s do not select targets
- At resolution, actions may revalidate targets and fizzle if invalid

## Multi-Target Behavior

- Multi-target types generate multiple actions

Examples:

all_enemies damage
- one damage action per enemy

all_entities effect
- one action per entity

## Edge Cases

No valid enemies:
- single_enemy → cannot play
- all_enemies → cannot play (recommended)

No valid entities:
- all_entities → cannot play (recommended)

Target becomes invalid after targeting:
- action fizzles during resolution

Board changes during resolution:
- resolved targets do not change
- each action validates independently

## Design Rules

- Targeting is declared on [[Card]] or enemy intent
- Targeting resolves once per play/intent
- No retargeting during action resolution
- No source-relative targeting logic

## Related
- [[Card]]
- [[Action]]
- [[Action Queue]]
- [[Enemy Phase]]
- [[Player Phase]]