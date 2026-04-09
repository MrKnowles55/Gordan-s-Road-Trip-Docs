## Purpose
- Stores the ordered draw pile for the player
- Provides cards to draw operations

## Core Rule
- Cards are always drawn from the top of the deck

## Data

cards
- Ordered list of cards (top to bottom)

owner
- Player reference

## Rules
- Deck order is preserved unless explicitly modified
- Deck may be empty
- A card exists in exactly one zone at a time
- Deck does not initiate draws, it only provides cards when requested

## Operations

draw()
- removes and returns the top card

shuffle(discard pile)
- moves all cards from [[Discard Pile]] into deck
- randomizes order
- empties discard

add(card, position)
- inserts a card into deck
- position must be defined (top, bottom, random, or index)

remove(card)
- removes specific card instance from deck

## Order of Operations

Draw Request:

1. Draw is requested via [[Action]]
2. if deck is empty:
	- attempt shuffle from [[Discard Pile]]
3. If deck is still empty:
	- draw action fizzles
4. else:
	- return the top card

## Interaction

- [[Action]]s request cards from deck
- [[Discard Pile]] supplies cards when deck is empty
- Cards move:
	- From deck to hand (draw)
	- from discard to deck (shuffle)

# Edge Cases
Both deck and discard are empty:
- Draw fails (fizzle)

Multiple Draw actions:
- Each draw checks deck state independently
- shuffle may occur mid-sequence

## Standardization Rules

- Shuffle only occurs when a draw is attempted on an empty deck
- One draw action is one card drawn
- Deck never auto shuffles outside of draw attempts.