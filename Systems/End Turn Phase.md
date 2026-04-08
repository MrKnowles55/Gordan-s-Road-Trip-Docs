
## Purpose
- Finalize player turn state before enemies act

## Order of Operations
1. Resolve end of turn effects (player owned)
2. Discard hand (except retained cards)
3. Clear turn based temporary states
4. Resolve death checks
5. Ensure [[Action Queue]] is Empty
6. Advance to [[Enemy Phase]]

## End of Turn Effects
- Relics
- Powers
- Status Effects

## Discard

- Move all non-retained cards from hand to discard
- Does <b>NOT</b> trigger "on discard" effects

## Temporary State Clear
- Remove effects marked "this turn"
- Reset per-turn counters and flags

## Related
- [[Player Phase]]
- [[Action Queue]]