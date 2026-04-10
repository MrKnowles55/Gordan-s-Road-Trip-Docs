## Purpose
- Holds cards currently available for the player to play
- Acts as the only valid zone for card play
## Data

cards
- Unordered collection of cards in hand

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

remove(card)
- removes specific card instance from hand

contains(card)
- checks if card is currently in hand

## Order of Operations

Draw into hand:
1. Card is drawn from [[Deck]]
2. If hand is not full:
	- move card to hand
3. If hand is full:
	- card is discarded

## Card Play

1. Card is selected from hand
2. 
