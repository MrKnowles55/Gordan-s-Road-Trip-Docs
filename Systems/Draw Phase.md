## Draw Count
- Default: 5 cards
- Modified by effects

## Order of Operations
1. Determine draw_count
2. Repeat draw_count times:
	a. If deck is empty:
		- If discard is empty -> stop drawing
		- Else shuffle discard into deck
	b. Draw top card from deck into hand
	c. Trigger on-draw effects for that card
3.  After all draws complete:
	- Trigger end of draw phase effects
4. Advance to [[Player Phase]]

## Rules
- Deck and discard both empty -> draw ends early
- Full hand -> drawn card is discarded (or exhausted, NEEDS DEFINED)
- Effects that modify draw mid-loop:
	- Increase -> Extend loop
	- Decrease -> does not retroactively remove draws

## Effect Timing
- On draw -> immediately after each card is drawn
- Global draw modifiers -> applied before loop
- End of draw effects ->after loop completes

## Related
- [[Deck]]
- [[Card]]
- [[Discard Pile]]
- [[Hand]]
- [[Status Effects]]
- [[Round Structure]]