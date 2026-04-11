## Purpose
- Stores cards after they are used or discarded
- Serves as the source for reshuffling into [[Deck]]

## Core Rule
- Cards enter discard only though defined transitions and remain until moved

## Data

cards
- Unordered collection of cards

owner
- Player reference

## Rules
- Order is not preserved or relied upon
- A card exists in exactly one zone at a time
- Discard does not initiate movement; other systems move cards into and out of it.

## Operations

add(card)
- moves card into discard

add_many(cards)
- moves multiple cards into discard

remove(card)
- removes specific card instance

remove_all()
- returns all cards (used for shuffling)


## Order of Operations
#### Common Flows

Card Played
1. Card finished play
2. If not exhausted:
	- Move card to discard

Player End Turn:
1. Hand discard step triggers
2. Each non-retained card is moved to discard

Shuffle:
1. [[Deck]] requests shuffle
2. All discard cards are removed
3. Passed into deck.shuffle()

## Edge Cases

Discard Empty:
- Shuffle produced empty deck
- subsequent draws fizzle

Card moved directly to another zone:
- bypasses discard

## Standardization Rules

- Discard is treated as unordered
- All reshuffles pull entire discard pile
- No partial shuffle 

## Related
- [[Deck]]
- [[Card]]
- [[Card Play Pipeline]]
- [[Hand]]
- [[Action]]
- [[Action Queue]]
- [[End Turn Phase]]