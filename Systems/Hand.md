## Purpose
- Holds cards currently available for the player to play
- Acts as the only valid zone for card play
## Data

cards
- Ordered collection of cards in hand

owner
- Player reference

max_size
- Maximum number of cards allowed in hand

## Rules
- A Card exists in exactly one zone at a time
- Hand size cannot exceed max_size
- Cards remain in hand until played, discarded, or otherwise removed

## Operations

add(card)
- Adds card to hand if space is available
- if full, card follows overflow rule (discarded)
- Card is inserted at the default hand position

remove(card)
- removes specific card instance from hand
- remaining cards preserve relative hand order

contains(card)
- checks if card is currently in hand

reorder(card, position)
- moves a card to a new hand position
## Order of Operations

#### Draw into hand
1. Card is drawn from [[Deck]]
2. If hand is not full:
	- move card to hand into default hand position
3. If hand is full:
	- card is discarded

#### Card Play

1. Card is selected from hand
2. [[Card Play Pipeline]] validates play
3. Card is removed from hand
4. Card Play continues

#### End of Turn Discard
1. Each card in hand is evaluated
2. If retain it stays
3. if ethereal it is exhausted
4. otherwise it is moved to the [[Discard Pile]]
	- It is not "discarded" but directly moved

## Standardization Rules
- Hand order is preserved unless explicitly changed
- Hand order controls display order
- One card draw is one attempt to add to hand
- If attempting to draw cards when hand is full they are immediately moved to [[Discard Pile]]
- Discard is applied per card, not in bulk
- Ethereal happens before other effects during [[End Turn Phase]]

## Related
- [[Deck]]
- [[Discard Pile]]
- [[Card]]
- [[Card Play Pipeline]]
- [[Player Phase]]
- [[Draw Phase]]
- [[End Turn Phase]]