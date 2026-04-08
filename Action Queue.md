## Purpose
 - Central resolver for all ordered gameplay effects
 - Ensures consistent, deterministic state changes
## Core Rule
- No ordered gameplay state changes resolve outside this queue

## Queue Model
- FIFO (First in, first out)
- Actions resolve strictly in the order they are added

# Action Definition
An action is a discrete, ordered gameplay effect that changes game state through formal resolution.

An action must:
- Have a source
- Optionally have a target
- Modify game state
- Depend on resolution order

Examples:
- Deal damage
- Gain block
- Draw card
- Apply status
- Heal
- Discard / Exhaust
- Energy gain/loss

Not Actions:
- Intent selection
- Target selection
- Condition checks
- UI Updates
- Pure calculations

# Action Structure

- source
- target
- type
- value
- modifiers

# Action Lifecycle
1. Action is created
2. Action is enqueued
3. Queue processes next action
4. Validate Action
5. Apply Modifiers
6. Apply primary effect
7. Trigger reactions
8. Enqueue resulting actions
9. Action completes

# Queue Interface (Conceptual)

enqueue(action)
- Add action to end of queue

process_next()
- Resolve next action in queue

process_all()
- Continue resolving until queue is empty

is_empty()
- Returns true if queue has no actions

clear()
-Empties queue (Combat end / reset only)

# Resolution Rules
- Only one action resolves at a time
- Actions resolve fully before the next begins
- New actions are appended to the end of the queue (Unless Reaction)
- Resolution order must remain deterministic
- No parallel resolution

# Validation
Before resolving an action:
- Check the source is valid
- Check target is valid
- Check action is still applicable

If invalid:
- Action fizzles (no effect)

# Reactions
Action resolution may trigger:
- On damage dealt
- on damage taken
- on draw
- on status applied
- on death

Rules:
- Reactions do NOT interrupt the current action
- Reactions enqueue new actions
- New actions resolve after current action completes (Not FIFO)

# Edge Cases
Dead target:
- Actions fizzle

Dead Source:
- Action may still resolve (depends on the action)

Draw with empty deck:
- Attempt shuffle discard into deck
- if still empty -> action fizzles

Full hand:
- Discards the card

# Standardization Rules
- Multi-hit attacks are multiple damage actions
- Draw X enqueues X draw actions

# Related
- [[Combat System]]
- [[Player Phase]]
- [[Enemy Phase]]
- [[Draw Phase]]
- [[Status Effects]]