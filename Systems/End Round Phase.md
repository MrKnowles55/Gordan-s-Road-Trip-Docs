## Purpose
- Final cleanup step after all enemies have acted
- Resolves round-based effect expiration before the next turn begins

## Order of Operations
1. Resolve end of round effects
2. Decrement round-based status durations
3. Remove expired statuses
4. Resolve death and cleanup checks
5. Confirm [[Action Queue]] is empty
6. Advance to [[Start Turn Phase]]


## Round Based Status Decrement
Decrement statuses that expire once per round
Examples:
- Weak
- Vulnerable
- Frail

## Rules
- Player and enemy round-based statuses decrement here
- Statuses reduced to 0 are removed
- This phase does not complete until the [[Action Queue]] is empty
- Resolves death cleanup if anything reached 0 HP

## Related
- [[Status Effects]]
- [[Action Queue]]
- [[Combat System]]
- [[Enemy System]]

