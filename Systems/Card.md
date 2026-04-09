
## Definition
- A player controlled object that defines a playable effect
- Cards do not directly modify game state
- Cards generate [[Action]]s when played


## Purpose
- Primary method of player interaction
- Converts player input into queued gameplay effects

## Core Data
id (string)
- Unique identifier

name (string)

type (enum)
- attack
- skill
- power
- status

cost (int)
 - Energy required to play

targeting (enum)
- none
- self
- single_enemy
- all_enemies
- all

effects (list)
- defines what actions will be generated when played

zone (enum)
- deck
- hand
- discard
- exhaust

tags (list)
- Classification labels used for grouping, filtering, and synergies

Examples:
- attack
- skill
- power
- starter
- strike
- status
- curse

flags (list or struct) *<b><i>(May become a list of bools?)</i></b>*
- Explicit behavior modifiers read by systems during execution

Examples:
- exhaust
- retain
- ethereal

owner (reference)
- Player

## Effect Model
Cards do <b>NOT</b> contain direct logic

Each effect defines what actions to create.

Example:
	effects:
	- type: damage
	- value : 6
	
This becomes:
	enqueue(Action: damage, value=6, source=player, target=enemy)

## Play Behavior (Abstract)
play(target)

1. Validate
	- Card is in hand
	- Player has enough energy
	- Target is valid per targeting rules
2. Pay cost
	- Reduce player energy
3. Generate actions
	- Convert effects to [[Action]]s
	- enqueue actions into [[Action Queue]]
4. Move card
	- Default: hand to discard
	- if exhaust: hand to exhaust

## Rules
- Cards cannot resolve effects directly
- Cards must go through [[Action Queue]]
- Cards must validate before generating actions
- Cards cannot be played if validation fails

## Targeting Rules
- targeting defines valid targets
- validation must enforce targeting rules
- no target required for "none" or "all"
- single_enemy requires exactly one valid enemy
- all_enemies effects all valid enemies

## Zone Rules
- A card exists in exactly one zone at a time
- Valid zones:
	- deck
	- hand
	- discard
	- exhaust
- Cards are only playable from hand

## Card Modifiers
These are discrete mechanics that alter card behavior

exhaust (bool)
- if true, card moves to exhaust instead of discard after play
- Happens after card actions

retain (bool)
- Card is not discard during [[End Turn Phase]]

ethereal (bool)
- If still in hand at [[End Turn Phase]], it is exhausted

cost_modification (int or rule)
- alters cost at time of play

play_restriction (ruleset)
- Conditions required to play
- Example:
	- only if target has status
	- only if player HP <50%

#### Where modifiers happen
Validation step  
→ play restrictions  
  
Cost step  
→ cost modifiers  
  
Action generation  
→ effect modifiers (double play, etc.)  
  
Post-play movement  
→ exhaust vs discard  
  
Player End Turn  
→ retain / ethereal

## Related
- [[Action]]
- [[Action Queue]]
- [[Targeting]]
- [[Hand]]
- [[Deck]]
- [[Discard Pile]]
- [[Energy System]]
- [[End Turn Phase]]