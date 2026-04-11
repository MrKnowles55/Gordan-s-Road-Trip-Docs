## Definition
- A discrete, ordered gameplay effect that changes game state through formal resolution

An action must:
- Have a source
- Optionally have a target
- Modify game state
- Depend on resolution order

## Purpose
- Standard unit of gameplay change
- Enforced through [[Action Queue]]
- Ensures consistent interaction between systems

## Data Structure

type (enum, <b>required</b>)
- damage
- block
- draw
- apply_status
- heal
- discard
- exhaust
- energy

source (id/reference, <b>required</b>)
- Entity creating the action (player, enemy, system)

target (id/reference, <b>optional</b>)
- Entity affected (can be null)

value (number, <b>optional</b>)
- magnitude of effect

payload (struct, <b>optional</b>)
- additional data depending on type

flags (struct, <b>optional</b>)
- behavior modifiers
Examples:
- is_aoe
- ignores_block

## Type Rules

- Type determines how resolve() behaves
- payload schema depends on type
- invalid combinations must be rejected during validation

Examples:

damage:
- requires target
- uses value

draw:
- does not require target
- represents exactly 1 card draw
- does not use value

apply_status
- requires target
- uses payload.status and payload.stacks

## Resolution

1. Validate
	- Source exists (if required)
	- Target exists (if required)
	- Action is still applicable
2. Apply modifiers (derived from state)
	- Query source state (e.g. Strength)
	- Query target state (e.g. Vulnerable)
	- Apply modifier rules in defined order
	- No modifiers are stored on the action
3. Apply primary effect
	- Modify Game state
4. Check resulting state
	- HP <= 0
	- Status thresholds
	- Other triggers
5. Trigger Reactions
	 - on damage dealt
	 - on damage taken
	 - on draw
	 - on status applied
	 - on death
6. Enqueue resulting actions
	- Reactions become new actions
7. Complete

## Queue Interaction

- Actions are enqueued into [[Action Queue]]
- Actions normally resolve in FIFO (first in, first out) order.
- Reactions generated during resolution are inserted before older pending actions
- Actions never interrupt the currently resolving action

## Rules

- Actions do not execute outside [[Action Queue]]
- Actions resolve one at a time
- Actions fully resolve before the next begins
- Actions may generate additional actions
- Actions must be deterministic

## Common Action Types
Damage
- Reduce HP

Block
- Increase Block value

Draw
- Move card from deck into hand

Apply Status
- adds debuff or buff

Heal
- Restores HP

Discard
- Moves card from hand to discard

Exhaust
- Removes card from combat

Energy
- Gain or lose energy

## Non-Actions

These do <b>NOT</b> use the Action System

- Intent selection
- Target selection
- Condition checks
- UI updates
- Pure Calculations

## Edge Cases
Invalid Target
- Action fizzles

Dead Source:
- Action may still resolve depending on type

Zero or negative value:
 - May fizzle or resolve depending on type

## Invalid Actions (Fizzle)

#### Definition
- An action that resolves but produces no effect due to failed validation or unmet conditions

#### When an action fizzles

An action fizzles if:
- Target is no longer valid
	Example: target is dead or removed

- Required state is missing
	Example: Draw with no cards in deck or discard

- Conditions are not met
	Example: apply effect to non-existent entity

#### Behavior
- Action still resolves through the queue
- No primary effect is applied
- No state is modified

#### Reactions
- Fizzled actions do NOT trigger on effect reactions
	Example: No "on damage" if damage did not occur

- Fizzled actions MAY trigger failure-specific reactions if defined
	- (Currently not designed for this)

#### Logging
- Fizzled actions should be traceable for debugging

#### Rule
- Fizzling is a safe failure, not an error
- It must never break queue processing
## Design Rules

- Multi-hit attacks are multiple queued actions
- Draw X are multiple queued draw actions
- No implicit batching of actions
- All interactions must route through actions

## Related
- [[Action Queue]]
- [[Combat System]]
- [[Card]]
- [[Enemy System]]
- [[Relics]]
- [[Status Effects]]



