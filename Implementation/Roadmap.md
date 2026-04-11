## COMBAT REWRITE IMPLEMENTATION NOTES

### Purpose

This note is not a design note.
The design already exists in the system notes.

This note is the implementation route for rebuilding combat in the project so the runtime actually matches the note structure already defined in:
- [[Combat System]]
- [[Round Structure]]
- [[Action Queue]]
- [[Action]]
- [[Card Play Pipeline]]
- [[Card]]
- [[Hand]]
- [[Targeting]]
- [[Energy System]]

The rewrite should follow the existing note architecture unless a note is explicitly wrong and updated first.

---

## What Already Exists Conceptually

The vault already defines a fairly clear runtime model.

The intended combat flow is:

[[Start Turn Phase]]
→ [[Draw Phase]]
→ [[Player Phase]]
→ [[End Turn Phase]]
→ [[Enemy Phase]]
→ [[End Round Phase]]

The intended gameplay rule is also already clear:

- gameplay state changes do not happen directly
- gameplay state changes become [[Action]]s
- [[Action]]s resolve through [[Action Queue]]
- cards do not directly modify state
- player phase does not resolve gameplay directly
- enemy phase does not resolve gameplay directly
- targeting resolves before action creation
- action resolution may revalidate and fizzle

That means the rewrite is not about inventing architecture.
It is about making the project actually obey the architecture already written down.

---

## Main Rewrite Goal

Replace the current prototype path:

input
→ direct mutation
→ visible result

with the intended path:

input / intent
→ validation
→ target resolution
→ action creation
→ enqueue
→ ordered resolution
→ follow-up checks
→ next phase

---

## What Must Be True When The Rewrite Is Done

### Runtime ownership

There must be one battle owner.
That owner controls:
- combat lifecycle
- phase progression
- queue lifecycle
- battle end checks
- turn ownership
- whether input is currently allowed

### Player interaction

Player input must only do three things:
- request card play
- request target selection
- request end turn

Player input must not:
- directly pay energy
- directly deal damage
- directly gain block
- directly discard cards
- directly end combat

### Enemy interaction

Enemy logic must only do two things:
- determine intent
- convert intent into action generation on enemy phase

Enemy logic must not:
- directly damage player
- directly advance battle state
- directly decide battle cleanup

### Card behavior

Cards must only define:
- cost
- targeting
- effect data
- tags / flags / metadata

Cards must not:
- directly change HP
- directly change block
- directly draw
- directly apply status

Cards generate actions.
They do not resolve gameplay.

### Action behavior

Actions must be:
- created
- enqueued
- resolved in order
- validated at resolution time
- allowed to fizzle safely
- allowed to generate more actions

Actions must be the only normal path for gameplay state change.

---

## Rewrite Strategy

Do not patch the prototype into compliance one piece at a time inside the same old path.

Instead:
- create a new combat runtime lane
- keep old prototype code temporarily as reference
- rebuild the runtime to match the vault notes
- delete old direct-mutation behavior only after the new route works

This should be done inside the same project, not by making a totally separate project.

---

## New Runtime Lane

Suggested ownership pieces:

- `obj_battle_controller`
- `scr_battle_init`
- `scr_battle_update`
- `scr_battle_begin_phase`
- `scr_battle_advance_phase`
- `scr_battle_check_end`
- `scr_action_create`
- `scr_action_validate`
- `scr_action_resolve`
- `scr_queue_enqueue`
- `scr_queue_process_next`
- `scr_queue_process_all`
- `scr_card_play_attempt`
- `scr_targeting_resolve`
- `scr_energy_can_pay`
- `scr_energy_pay`

The exact names can change.
The important part is separating ownership by responsibility.

---

## Implementation Order

The rewrite should be built in dependency order, not feature order.

Correct order:

1. Battle shell
2. Phase progression
3. Action struct + queue
4. Damage / block actions
5. Current simple combat routed through queue
6. Card play pipeline
7. Hand / draw / discard integration
8. Enemy phase action generation
9. Battle-end flow
10. Statuses / modifiers / reactions
11. Content expansion

If this order is violated, the project will keep reintroducing prototype shortcuts.

---

## PHASE 1 — BUILD THE BATTLE SHELL

### Objective

Create the new battle owner that matches the existing notes.

This is not yet the full combat system.
This is the runtime container that all later systems plug into.

### Required controller state

The battle controller should know:

- current phase
- phase index or enum
- player reference
- enemy list/reference
- current round number
- whether battle is active
- whether input is locked
- whether queue is resolving
- pending victory
- pending defeat

At minimum, phase should reflect the note structure:

- START_TURN
- DRAW
- PLAYER
- END_TURN
- ENEMY
- END_ROUND
- VICTORY
- DEFEAT

Do not collapse all of this into generic “player turn / enemy turn” if the vault already defines the more specific structure.

### Build tasks

- [x] Create `obj_battle_controller`
- [x] Give it combat actor references
- [x] Add battle-active flag
- [x] Add round counter
- [x] Add explicit phase enum matching the vault
- [x] Add debug display for current phase
- [x] Add phase transition function
- [x] Add battle init function
- [ ] Add battle end check function
- [ ] Make the room use the new controller as combat owner

### Done when

- [x] one object owns combat flow
- [x] current phase is always known
- [x] battle starts from explicit init
- [x] phase progression is centralized
- [ ] victory / defeat states exist even if still simple

### Important rule

Do not let UI objects decide which phase comes next.
Do not let enemy instances advance combat on their own.

---

## PHASE 2 — IMPLEMENT THE ROUND STRUCTURE EXACTLY

The vault already defines the round flow.
The project should follow it directly.

### Intended flow

#### Start Turn Phase
- increment round counter
- reset player energy to base
- remove block unless retained
- trigger start-of-turn effects
- advance to draw

#### Draw Phase
- draw cards into hand
- obey hand-size rules
- resolve draw through queue if following the note literally
- advance to player phase

#### Player Phase
- wait for input
- allow card play attempts
- allow end turn request
- do not resolve gameplay directly here

#### End Turn Phase
- process hand cleanup
- retain / ethereal / discard handling
- remove temporary this-turn effects
- advance to enemy phase

#### Enemy Phase
- resolve enemies left to right
- trigger enemy start-of-turn effects
- execute current intent through action generation
- determine next intent
- advance to end round

#### End Round Phase
- resolve round cleanup
- advance to next start turn

### Build tasks

- [x] Create explicit phase handlers for each note-defined phase
- [ ] Do not skip Draw / End Turn / End Round as “implied”
- [ ] Make each phase do only its own responsibilities
- [x] Keep transition logic in controller, not scattered in actors

### Done when

- [x] phase order matches the vault
- [ ] each phase has one clear purpose
- [ ] the controller can step through a whole round without prototype shortcuts

---

## PHASE 3 — BUILD ACTION AS THE RUNTIME UNIT

The vault already defines [[Action]] as the standard unit of gameplay change.
This must become literal runtime behavior.

### Required action fields

Based on the note, action data should support:

- `type`
- `source`
- `target`
- `value`
- `payload`
- `flags`

Do not store derived modifiers on the action if following the current note strictly.
The note says modifiers should be queried from current state during resolution.

### Supported first action types

Implement these first:
- damage
- block
- draw
- energy

Do not start with exotic types.
Prove the core path first.

### Build tasks

- [ ] Create action constructor/helper
- [ ] Validate required fields per action type
- [ ] Define type rules
- [ ] Create action debug print/log
- [ ] Ensure action data is deterministic and serializable enough for debugging

### Done when

- [ ] actions can be created from player and enemy systems
- [ ] action data is consistent
- [ ] invalid action definitions are rejected safely

---

## PHASE 4 — BUILD THE ACTION QUEUE TO MATCH THE NOTES

The queue note is one of the most important notes in the vault.
The implementation should match it closely.

### Rules already defined in notes

From the vault:
- one action resolves at a time
- actions fully resolve before the next
- no parallel resolution
- actions may generate more actions
- actions can fizzle
- multi-hit means multiple damage actions
- draw X means multiple draw actions

One important note conflict exists:
`Action Queue.md` says reactions enqueue after current action completes and describes appending behavior.
`Action.md` says reactions are inserted before older pending actions.

This must be resolved before coding reaction ordering.
For the first rewrite pass, reactions should probably be postponed or forced into one clear rule.

### Initial implementation rule

For first-pass rewrite:
- reactions do not interrupt current action
- reactions are added in a single deterministic way
- do not build complicated interrupt logic yet

### Build tasks

- [ ] Create queue storage
- [ ] Add enqueue
- [ ] Add dequeue
- [ ] Add `process_next`
- [ ] Add `process_all`
- [ ] Add safe clear/reset
- [ ] Add queue-owned resolving flag
- [ ] Add fizzle-safe behavior
- [ ] Add debug logs for enqueue and resolve

### Done when

- [ ] damage can be queued
- [ ] block can be queued
- [ ] queue resolves in deterministic order
- [ ] queue continues safely after fizzles
- [ ] resolution never bypasses the queue

---

## PHASE 5 — ROUTE CURRENT SIMPLE COMBAT THROUGH ACTIONS

Before full cards are built, convert the current simple test interactions into queue-backed requests.

This is the bridge from prototype to architecture.

### Old path that must die

Current temporary interactions likely do things like:
- spend energy directly
- deal damage directly
- gain block directly
- enemy directly attacks

All of that must stop.

### Temporary replacement path

For now, use request functions such as:
- `battle_request_basic_attack`
- `battle_request_basic_block`
- `battle_request_end_turn`

These are still temporary.
They are not the final card system.
But they let you prove the queue works before cards exist.

### Build tasks

- [ ] Remove direct HP mutation from temporary player input
- [ ] Remove direct block mutation from temporary player input
- [ ] Remove direct enemy damage mutation
- [ ] Validate turn ownership before creating actions
- [ ] Validate that battle is not locked/resolving
- [ ] Create damage/block actions from requests
- [ ] Route those through queue

### Done when

- [ ] the prototype buttons still function as temporary tools
- [ ] but they no longer directly change game state
- [ ] all current basic combat effects go through action creation and queue resolution

---

## PHASE 6 — IMPLEMENT ENERGY TO MATCH CARD PLAY RULES

The vault is clear that:
- a card must fully validate before cost is paid
- cost is paid before actions are generated
- cost is evaluated at play time

That means energy handling must be implemented with card play ordering in mind, not as a random direct subtraction.

### Build tasks

- [ ] Add `current_energy`
- [ ] Add `base_energy`
- [ ] Reset energy in Start Turn Phase
- [ ] Implement `can_pay(cost)`
- [ ] Implement `pay(cost)`
- [ ] Make payment happen after play validation
- [ ] Make energy changes visible immediately for subsequent plays

### Important implementation rule

Temporary attack/block request buttons may spend energy for testing, but full card play must use the card play order from the vault:
1. validate
2. target
3. pay cost
4. remove from hand
5. generate actions
6. enqueue
7. move card to destination

---

## PHASE 7 — IMPLEMENT THE CARD PLAY PIPELINE LITERALLY

The vault already has the sequence.
Follow it directly.

### Required play order

1. Player selects card from hand
2. Check card is still in hand
3. Check card is playable
4. Resolve targeting requirements
5. Select target if required
6. Validate target(s)
7. Pay cost
8. Remove card from hand
9. Generate effects from card data
10. Generate actions using resolved target data
11. Enqueue actions
12. Move card to destination

Do not collapse this into “click card, effect happens.”

### Build tasks

- [ ] Create card definition data
- [ ] Create card instance/zone data if using separate runtime instances
- [ ] Make hand the only valid play zone
- [ ] Implement play attempt function
- [ ] Implement in-hand check
- [ ] Implement playability validation
- [ ] Implement targeting resolution
- [ ] Implement cost payment timing
- [ ] Remove card from hand at commit point
- [ ] Convert card effects to action data
- [ ] Enqueue actions
- [ ] Move card to discard or exhaust based on flags

### Done when

- [ ] one attack card works
- [ ] one defend card works
- [ ] card play obeys the exact validation/payment/generation order from the vault

---

## PHASE 8 — IMPLEMENT HAND / DRAW / DISCARD TO MATCH THE NOTES

These notes are already detailed enough to guide implementation.

### Hand requirements from notes

- cards exist in exactly one zone
- hand has ordered collection
- hand order controls display order
- draw adds if not full
- if full, drawn cards go to discard
- end turn moves or exhausts cards individually

### Build tasks

- [ ] Represent zones explicitly
- [ ] Ensure one card is in one zone only
- [ ] Implement draw into hand
- [ ] Implement hand max size
- [ ] Implement overflow-to-discard rule
- [ ] Implement remove-from-hand on commit
- [ ] Preserve hand order unless explicitly changed
- [ ] Implement end-turn hand cleanup per card

### End-turn cleanup rule from notes

For each card in hand:
- retain stays
- ethereal exhausts
- otherwise move to discard pile

That should be implemented exactly, not as one bulk “clear hand” shortcut.

---

## PHASE 9 — IMPLEMENT TARGETING AS PRE-ACTION RESOLUTION

The targeting note is already strong.
Use it directly.

### Required targeting rules

- targeting resolves before action creation
- actions receive target or targets
- actions do not perform target selection
- multi-target effects create multiple actions
- invalid targets at resolution time fizzle independently

### Build tasks

- [ ] Implement targeting enum support:
  - [ ] none
  - [ ] self
  - [ ] player
  - [ ] single_enemy
  - [ ] all_enemies
  - [ ] all_entities
- [ ] Create targeting resolve helper
- [ ] Return one of:
  - [ ] target
  - [ ] targets
  - [ ] no target
- [ ] Make card play and enemy intents use same targeting resolution path
- [ ] Revalidate targets during action resolution and fizzle if necessary

### Done when

- [ ] a single-target attack card works
- [ ] a self-target block card works
- [ ] an all-enemies attack can generate one action per enemy

---

## PHASE 10 — IMPLEMENT ENEMY PHASE TO MATCH THE NOTE

The enemy phase note already defines the order.

### Required enemy phase order

1. Resolve enemies left to right
2. Skip dead or unable enemies
3. Trigger enemy start-of-turn effects
4. Execute current intent
5. Resolve via action queue
6. Determine next intent
7. Advance to End Round Phase

### Important implementation rule

Enemy intent should already exist before the phase begins.
Enemy action uses current intent.
Next intent is chosen after acting.

That means enemy state should likely store:
- `current_intent`
- `next_intent`

### Build tasks

- [ ] Add enemy list ordering
- [ ] Add current/next intent storage
- [ ] Execute current intent through action generation only
- [ ] Do not allow direct enemy damage
- [ ] Choose next intent after acting
- [ ] Update UI after next intent selection

### Done when

- [ ] enemy acts through the queue
- [ ] enemy order is deterministic
- [ ] next intent becomes visible after acting

---

## PHASE 11 — DEFER REACTIONS UNTIL CORE FLOW WORKS

The notes already include reactions, but there is a note conflict about their queue insertion order.

Do not implement full reaction complexity until:
- battle phases work
- queue works
- card play works
- enemy phase works

### Temporary rule

For the first working rewrite:
- allow action resolution without reaction depth
- or implement only one simple deterministic reaction rule
- update the notes first if reaction ordering is undecided

### Note conflict to resolve later

Current note mismatch:
- `Action Queue.md` suggests append behavior
- `Action.md` says reactions insert before older pending actions

Pick one later and update the vault before implementing that part deeply.

---

## PHASE 12 — BATTLE END AND CLEANUP

This must happen after core combat loop works.

### Build tasks

- [ ] Detect victory when all enemies are dead
- [ ] Detect defeat when player is dead
- [ ] Lock input once outcome is final
- [ ] Finish or clear queue safely
- [ ] stop additional phase progression
- [ ] clean temporary combat state
- [ ] transition out of combat cleanly

### Done when

- [ ] battle can start, loop, and end without leftover state corruption

---

## Immediate Build Checklist

This is the actual order to start coding from the current project state.

- [ ] Create `obj_battle_controller`
- [ ] Implement note-accurate phase enum
- [ ] Implement Start Turn → Draw → Player → End Turn → Enemy → End Round flow
- [ ] Create action data struct
- [ ] Create action queue
- [ ] Implement damage action
- [ ] Implement block action
- [ ] Route temporary player attack through queue
- [ ] Route temporary player block through queue
- [ ] Route enemy attack through queue
- [ ] Implement energy reset in Start Turn
- [ ] Implement hand / draw / discard basics
- [ ] Implement card play validation pipeline
- [ ] Replace temporary attack/block controls with real cards
- [ ] Implement enemy current_intent / next_intent flow
- [ ] Add battle end / cleanup
- [ ] Only then begin statuses / reactions / modifiers

---

## Known Note Issues To Resolve Before Or During Rewrite

These are vault-level issues, not necessarily project bugs.

### Issue 1 — Reaction queue ordering conflict

`[[Action Queue]]` and `[[Action]]` currently describe reaction insertion differently.

This needs a single rule before reaction-heavy systems are built.

### Issue 2 — Draw as queue action vs draw phase flow

The notes say draw is an action type, but the round structure also has a dedicated draw phase.
That is workable, but implementation should be explicit:
the Draw Phase should probably enqueue draw actions rather than bypassing the queue.

### Issue 3 — End turn destination wording

The notes distinguish “move to discard” from “discarded.”
That is fine, but implementation should be consistent and use one vocabulary for:
- discard as an action/effect
- end-turn move-to-discard as zone cleanup

### Issue 4 — Missing explicit combat system ownership note

The vault has strong component notes, but the implementation rewrite should treat the battle controller as the owner of:
- phase progression
- queue lifecycle
- combat actor registry
- battle end checks
- phase transitions

If that is not already written in `[[Combat System]]`, it should be added.

---

## Hard Implementation Rules

- [ ] No gameplay state changes outside the action system, except phase bookkeeping and pure setup/cleanup
- [ ] Cards do not directly resolve gameplay
- [ ] Player phase does not resolve gameplay
- [ ] Enemy phase does not resolve gameplay
- [ ] Targeting resolves before action creation
- [ ] Actions revalidate at resolution and may fizzle
- [ ] One combat owner controls the loop
- [ ] Prototype direct-mutation paths should be removed, not preserved

---

## Progress

- [ ] Battle controller exists
- [ ] Full round structure exists
- [ ] Action queue works
- [ ] Basic damage/block actions work
- [ ] Temporary test combat routes through queue
- [ ] Energy system matches card-play order
- [ ] Hand / draw / discard works
- [ ] Card play pipeline works
- [ ] Enemy phase works through queue
- [ ] Battle end / cleanup works
- [ ] Statuses / reactions / modifiers begin