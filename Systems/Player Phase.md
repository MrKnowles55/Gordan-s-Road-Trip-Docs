### Purpose
- Handles player interaction during their turn
- Allows playing cards and ending the turn

## Core Rule
- Player actions are processed sequentially until the turn is ended

## State

active
- player turn is ongoing

## Order of Operations

1. Enter player phase
2. Loop until turn ends:

   a. Wait for player input

   b. If player selects a card:
      - Execute [[Card Play Pipeline]]

   c. If player ends turn:
      - exit loop

3. Advance to [[Player End Turn]]

## Input Handling

- Player may:
  - select a card to play
  - select a target (if required)
  - end turn

- Input must be validated before execution

## Card Play

- All card plays go through [[Card Play Pipeline]]
- Player phase does not implement play logic directly

## Turn End

- Player can end turn at any time
- Turn must end if no valid actions remain (optional rule)

## Interaction

- Uses [[Hand]] for available cards
- Uses [[Energy System]] for cost validation
- Uses [[Targeting]] for target selection
- Sends card plays to [[Card Play Pipeline]]

## Edge Cases

No playable cards:
- player may still end turn

Invalid input:
- ignored

Concurrent inputs:
- processed one at a time

## Standardization Rules

- No gameplay state changes occur outside [[Action Queue]]
- Player phase only initiates actions, it does not resolve them

## Related
- [[Hand]]
- [[Card Play Pipeline]]
- [[Player End Turn]]
- [[Action Queue]]