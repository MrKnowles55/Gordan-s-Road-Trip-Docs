
## Purpose
- Finalize player turn state before enemies act

## Order of Operations
1. Resolve end of turn effects (player owned)
2. Ethereal cards exhaust
3. Move cards to discard pile from hand (except retained cards)
4. Clear turn based temporary states
5. Resolve death checks
6. Ensure [[Action Queue]] is Empty
7. Advance to [[Enemy Phase]]

## End of Turn Effects
- Ethereal
- Relics
- Powers
- Status Effects

## Discard Step

- Move all non-retained cards directly from hand to Discard Pile
- Does <b>NOT</b> trigger "on discard" effects

## Temporary State Clear
- Remove effects marked "this turn"
- Reset per-turn counters and flags

## Related
- [[Player Phase]]
- [[Action Queue]]