## Order of Operations
1. Turn counter += 1
2. Reset player energy to base energy (default = 3)
3. remove all block (unless retain effect exists)
4. Trigger start of turn effects

## Start of Turn Effects
- Relics
- Powers
- Status Effects
## Rules
- Block resets BEFORE Start of turn effects
- Energy resets BEFORE effects that modify energy
- Effects resolve in deterministic order (define later)

## Edge Cases
 - Retain block -> skip block removal
 - Energy Modifiers -> Applied after reset

## Related
- [[Combat System]]
- [[Status Effects]]
- [[Relics]]