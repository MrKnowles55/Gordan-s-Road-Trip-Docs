### Purpose
- Tracks and enforces the resource required to play cards

## Core Rule
- A card cannot be played unless sufficient energy is available

## Data

current_energy
- amount of energy available this turn

base_energy
- default energy at start of turn

## Rules

- Energy is spent when a card is played
- Energy cannot drop below 0
- Energy resets at the start of each player turn
- Energy modifications are applied at time of use, not stored on cards

## Operations

can_pay(cost)
- returns true if current_energy >= cost

pay(cost)
- subtracts cost from current_energy

gain(amount)
- increases current_energy

set(amount)
- sets current_energy (used at turn start)

## Order of Operations

Start of turn:

1. Set energy = base_energy
2. Apply any start-of-turn modifiers

Card play:

1. Determine effective cost
2. Check can_pay(cost)
3. If true:
   - pay(cost)
4. Else:
   - play fails

## Interaction

- Used by [[Card Play Pipeline]] for validation and cost payment
- Modified by card effects and other systems
- Reset during [[Start Turn Phase]]

## Edge Cases

Zero-cost cards:
- can always be played

Negative cost (if allowed):
- treated as gain or clamped to 0 (define rule)

Energy modified mid-turn:
- affects subsequent plays immediately

## Standardization Rules

- Cost is evaluated at time of play
- Payment occurs before action generation
- No deferred or partial payment

## Related
- [[Card]]
- [[Card Play Pipeline]]
- [[Start Turn Phase]]
- [[Player Phase]]