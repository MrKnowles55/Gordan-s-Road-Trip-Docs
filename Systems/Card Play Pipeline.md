## Purpose
- Defines the exact sequence for playing a card
- Converts a card in hand into generated [[Action]]s

## Core Rule
- A card must fully validate before cost is paid or actions are generated

## Order of Operations

1. Player selects card from [[Hand]]
2. Check card is still in hand
3. Check card is playable (cost and restrictions)
4. Resolve targeting requirements
5. Select target if required
6. Validate target or targets
7. Pay Cost
8. Remove card from hand
9. Generate effects from card data
10. Generate [[Action]]s using resolved target data
11. Enqueue actions into [[Action Queue]]
12. Move card to destination
	- default: [[Discard Pile]]
	- if exhaust: exhaust
t
## Validation

A card play is valid only if
- Card is in hand
- Player can pay cost
- Targeting requirements are satisfied
- Selected target is valid
- play restrictions are satisfied

If validation fails:
- card play does not proceed

## Targeting
- Targeting is resolved during card play
- Target selection occurs before cost is paid
- [[Action]]s receive resolved target data
- [[Action]]s do not perform target selection

## Cost
- cost is paid after validation
- Cost modifications are applied at time of payment

## Action Generation

- Card effects do not modify game state directly
- Card effects generate [[Action]]s
- Multi target effects generate one action per target

## Destination
- After actions are generated:
- default : move card to [[Discard Pile]]
- if exhaust: move card to exhaust

## Edge Cases

Target becomes invalid before cost is paid:
- Play fails

Target becomes invalid after actions are enqueued:
- Action fizzles during resolution

Card removed from hand before commit
- Play fails

## Standardization Rules
- Validation occurs before cost payment
- Cost payment occurs before action generation
- Actions are generated before card movement
- Card play commits when cost is paid and card is removed from hand

## Related
- [[Card]]
- [[Hand]]
- [[Targeting]]
- [[Energy System]]
- [[Action]]
- [[Action Queue]]
