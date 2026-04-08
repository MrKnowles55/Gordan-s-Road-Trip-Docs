# Order of Operations
1. Resolve enemies in position order (left to right)
	- If dead -> skip
	- if stunned/unable to act -> skip action
2.  Trigger start of turn effects
	- Relics
	- Powers
	- Status Effects
3. Execute intent
	- use previously determined intent
	- Enqueue actions(s) into [[Action Queue]]
	- Resolve via queue (no direct execution)
4. Trigger on action / on hit effects
	- Thorns, retaliation, etc.
5. Determine next intent
	- Based on AI rules / pattern
6. Advance to [[End Round Phase]]

## Rules
- enemies intents are determined AFTER acting, used NEXT turn
- No enemy can act twice in the same phase
- All actions resolve through [[Action Queue]]
- Order is strictly left to right

## Intent System
-  Each enemy always has
	- current_intent (used this turn)
	- next_intent (determined this phase)
- UI displays next_intent after phase completes

## Edge Cases
- Enemy dies mid phase
	- Remaining actions for that enemy are cancelled
- Enemy spawned mid phase
	- Does NOT act this turn
- Multi hit actions
	- Each hit is its own queued action

## Effect Timing
- Start of turn effects -> Before action
- Action Resolution -> via queue
- Next intent selection -> after action

## Related
- [[Enemy System]]
- [[Action Queue]]
- [[Status Effects]]
- [[Combat System]]

