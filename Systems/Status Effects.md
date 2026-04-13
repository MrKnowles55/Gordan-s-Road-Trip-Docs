### Purpose
- Defines the framework for how statuses behave in combat
- Standardizes timing, stacking, and expiration

## Core Rule
- Status effects are executed by systems at defined timing hooks, not by the status itself

## Categories

duration_based
- decrements at a defined timing step
- example: Weak, Vulnerable, Frail

persistent
- remains until explicitly removed
- example: Strength, Dexterity

triggered
- activates when a specific event occurs
- example: on damage taken, on draw

rule_modifying
- changes how another system behaves
- example: retain block, cost modification

## Timing Hooks

Statuses may define one or more:

- start_turn
- end_player_turn
- end_enemy_turn
- end_round
- before_action
- after_action
- on_draw
- on_discard
- on_damage_dealt
- on_damage_taken
- on_death

## Rules

- Every status must define:
  - category
  - timing hooks
  - stacking rule
  - expiration rule

- Statuses are read and applied by:
  - phases
  - action resolution
  - card play pipeline

## Order of Operations

1. A system reaches a timing hook
2. Relevant statuses are collected from the affected entity
3. Matching statuses are applied or checked
4. If duration-based:
   - decrement if this hook applies
5. If expired:
   - remove status

## Stacking Rules

add_stacks
- stacks += new value

refresh_duration
- replace remaining duration

replace_if_higher
- keep higher value

non_stacking
- ignore or refresh only

## Expiration Rules

A status expires when:

- stacks reach 0
- its condition becomes false
- it is explicitly removed
- combat ends

## Interaction

- Statuses modify behavior in:
  - [[Action]] resolution
  - [[Card Play Pipeline]]
  - phase processing

## Standardization Rules

- Status logic is not executed independently
- All behavior is driven by timing hooks
- Status definitions must be consistent across systems

## Related
- [[Status]]
- [[Action]]
- [[Start Turn Phase]]
- [[Player End Turn]]
- [[Enemy Phase]]
- [[End of Round]]