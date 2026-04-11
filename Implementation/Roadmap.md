
## COMBAT REWRITE OVERVIEW

This rewrite is not a full project restart.

The goal is to rebuild the combat runtime cleanly inside the same project while keeping the current project as a reference source for visuals, naming, rough flow, and any useful placeholder behavior.

The current build already proved a few important things:
- a combat scene can exist
- a player and enemy can appear
- turns can advance
- basic combat interactions feel testable

That means the prototype did its job.

What it did **not** do is establish correct architecture.

Right now, the project still behaves like a prototype:
- UI triggers gameplay state changes directly
- gameplay effects resolve immediately instead of through a formal system
- enemy behavior is likely using its own direct path
- turn flow exists, but is not yet the central owner of all battle resolution
- the project is interaction-first instead of resolution-first

That is the reason for the rewrite.

The rewrite should follow this principle:

**Do not keep extending the prototype path.**
**Build a clean combat path beside it, then replace the old path once the new one is working.**

That means:
- keep the project
- keep useful assets and visuals
- keep the repo
- keep old code as reference temporarily
- create a new battle implementation lane
- move system by system in dependency order
- delete the old path only after the new path is stable

---

## BIG PICTURE BUILD ORDER

The rewrite should move in this order:

1. Build a clean battle shell
2. Build a formal action pipeline
3. Route current simple combat through the action pipeline
4. Replace prototype buttons with proper card play flow
5. Make enemies use the same action path
6. Add persistent combat rules like statuses and modifiers
7. Finish victory, defeat, cleanup, and encounter exit
8. Expand into run systems only after combat is stable

This order matters because each stage depends on the stage before it.

Examples:
- cards should not be built before action resolution exists
- enemy logic should not stay on a separate direct-damage path
- statuses should not be added until there is a trustworthy resolution pipeline
- run-level progression should not be built on unstable combat

---

## WHAT TO KEEP VS WHAT TO REBUILD

### Keep
- the current project
- sprites and visual assets
- room setup if still useful
- debug visuals that still help
- naming conventions that still make sense
- old prototype code as temporary reference

### Rebuild
- combat controller ownership
- turn flow ownership
- action resolution path
- player action request flow
- enemy action execution flow
- card play flow
- victory / defeat / cleanup flow

### Do Not Do
- do not keep patching the current direct button logic forever
- do not try to evolve prototype shortcuts into final architecture
- do not add lots of content before the runtime backbone is clean
- do not add statuses, relics, or advanced systems before the action pipeline is trustworthy

---

## ARCHITECTURAL TARGET

By the end of the rewrite, combat should work like this:

input or AI intent  
→ request  
→ validation  
→ action generation  
→ enqueue  
→ ordered resolution  
→ post-resolution checks  
→ turn progression  
→ victory/defeat checks

Not like this:

button click  
→ immediately change HP/block/energy

That is the core architectural shift.

The rewrite is really about moving from:

**direct mutation**
to
**formal resolution**

---

## NEW COMBAT LANE

Build the new system beside the old one.

Suggested new core pieces:
- `obj_battle_controller`
- `scr_battle_init`
- `scr_battle_update`
- `scr_battle_request_*`
- `scr_action_create_*`
- `scr_action_queue_*`
- `scr_action_resolve_*`
- `scr_actor_*`
- `scr_card_*`
- `scr_status_*` later

The point is not the exact names.
The point is that the new lane has clear ownership and clear boundaries.

---

## PHASE 1 — BATTLE SHELL

### Objective
Create a clean battle owner that can run a fight without relying on prototype shortcuts.

### What This Phase Must Establish
- one object owns battle state
- one object advances battle flow
- player turn and enemy turn are explicit
- battle start and battle end are explicit
- victory and defeat states exist, even if still simple
- no UI object is responsible for combat state ownership

### Core Questions This Phase Answers
- who owns the battle?
- where does turn progression live?
- where do battle-wide checks happen?
- where is combat initialized?
- where is combat cleaned up?

### Build Tasks
- [x] Create `obj_battle_controller`
- [ ] Give it explicit battle states:
  - [ ] START
  - [ ] PLAYER_TURN
  - [ ] ENEMY_TURN
  - [ ] VICTORY
  - [ ] DEFEAT
  - [ ] CLEANUP if needed
- [ ] Create explicit turn/phase variables
- [ ] Create battle init script
- [ ] Spawn or bind player + enemy combat actors
- [ ] Give battle controller ownership of:
  - [ ] current turn owner
  - [ ] battle state
  - [ ] turn transitions
  - [ ] victory/defeat checks
- [ ] Make the fight enter a stable loop:
  - [ ] start battle
  - [ ] player turn
  - [ ] enemy turn
  - [ ] repeat
- [ ] Remove any requirement that UI objects manage battle flow

### Done When
- [ ] battle starts predictably
- [ ] battle state is readable and centralized
- [ ] turns alternate in one place
- [ ] controller can detect battle over conditions
- [ ] current combat no longer depends on prototype flow hacks

### Notes
Do not worry about cards yet.
Do not worry about statuses yet.
Do not worry about nice effects yet.

This phase exists to establish clean ownership.

---

## PHASE 2 — ACTION PIPELINE

### Objective
Create the formal path through which all gameplay state changes must pass.

This is the most important phase in the rewrite.

### What This Phase Must Establish
- all real gameplay effects are actions
- actions are created before they resolve
- actions resolve in order
- no combat state changes happen outside that path

### Why This Comes Before Cards
Cards are not the fundamental unit.
Ordered gameplay resolution is the fundamental unit.

Without this phase, cards will just become prettier buttons that still do direct mutation.

### Core Questions This Phase Answers
- what is an action?
- who owns the queue?
- how are actions ordered?
- how is action data validated?
- where do damage/block/status/draw effects resolve?
- when do death checks happen?

### Build Tasks
- [ ] Define action struct format

Example fields to decide:
- [ ] `type`
- [ ] `source`
- [ ] `target`
- [ ] `value`
- [ ] `payload` if needed
- [ ] `flags` if needed
- [ ] `id` if useful for debugging

- [ ] Decide queue structure:
  - [ ] array
  - [ ] ds_queue
- [ ] Put queue ownership on battle controller
- [ ] Create:
  - [ ] enqueue function
  - [ ] dequeue function
  - [ ] peek function if useful
  - [ ] process function
  - [ ] clear/reset function
- [ ] Decide whether resolution is:
  - [ ] instant for now
  - [ ] or step-based to allow future animation-aware processing
- [ ] Implement first action type:
  - [ ] DAMAGE
- [ ] Implement second action type:
  - [ ] GAIN_BLOCK
- [ ] Add post-resolution checks:
  - [ ] HP <= 0
  - [ ] death state
  - [ ] battle over check
- [ ] Add debug logging:
  - [ ] enqueue log
  - [ ] resolve log
  - [ ] final state log if useful

### Rules To Lock In
- [ ] no direct HP changes outside action resolution
- [ ] no direct block changes outside action resolution
- [ ] queue resolves one action at a time
- [ ] all state changes should be attributable to a resolved action

### Done When
- [ ] actions can be created cleanly
- [ ] actions resolve in order
- [ ] damage goes through queue
- [ ] block gain goes through queue
- [ ] death is handled through formal resolution flow
- [ ] you can look at a combat result and explain which action caused it

### Notes
This phase is the backbone.
Do not rush past it.

---

## PHASE 3 — REMOVE DIRECT MUTATION FROM PROTOTYPE INPUT

### Objective
Use the action system to replace the current shortcut behavior.

### What This Phase Must Establish
Current simple actions still exist, but they no longer directly mutate state.

Instead, prototype controls should become request sources only.

### Desired Flow
Old:
attack button  
→ spend energy  
→ directly damage enemy

New:
attack button  
→ request basic attack  
→ validate request  
→ create action  
→ enqueue  
→ resolve

### Core Questions This Phase Answers
- who is allowed to request gameplay?
- who validates whether that request is legal?
- who turns a request into an action?
- how does energy spending fit into the formal path?

### Build Tasks
- [ ] Replace direct button behavior with battle request functions
- [ ] Create request functions such as:
  - [ ] `battle_request_basic_attack()`
  - [ ] `battle_request_basic_block()`
  - [ ] `battle_request_end_turn()`
- [ ] Move validation into battle logic:
  - [ ] enough energy?
  - [ ] correct turn owner?
  - [ ] battle not locked/busy?
  - [ ] valid target exists?
- [ ] Make validated requests create formal actions
- [ ] Route energy spending through the proper path
- [ ] Ensure old UI objects do not directly touch HP/block
- [ ] Treat buttons as temporary debug input, not final architecture

### Done When
- [ ] prototype buttons still work functionally
- [ ] but they now only request actions
- [ ] HP/block/energy no longer change directly from UI code
- [ ] battle controller and action system own the resolution path

### Notes
This is the bridge phase between prototype and real game architecture.

---

## PHASE 4 — CARD PLAY LOOP

### Objective
Replace prototype controls with actual card-driven gameplay flow.

### What This Phase Must Establish
Cards become runtime entities with a real play pipeline.

A card is not just “an effect.”
A card must pass through:
- availability
- hand presence
- cost validation
- target selection if needed
- effect generation
- action creation
- discard/exhaust movement

### Core Questions This Phase Answers
- what is a card definition?
- what is a card instance?
- where is hand state stored?
- what happens when a card is clicked?
- when is a card considered legally playable?
- when does a card leave the hand?

### Build Tasks
- [ ] Define card definition format
- [ ] Define card instance format if separate
- [ ] Create draw pile
- [ ] Create hand container
- [ ] Create discard pile
- [ ] Implement draw logic
- [ ] Implement hand population
- [ ] Implement card UI display from hand state
- [ ] Implement play request path
- [ ] Validate:
  - [ ] enough energy
  - [ ] card can currently be played
  - [ ] target requirement satisfied
- [ ] Convert card effect into actions
- [ ] Enqueue actions
- [ ] Move played card to discard
- [ ] Refresh hand/UI state after play

### First Cards To Support
- [ ] one basic attack card
- [ ] one basic defend card
- [ ] optionally one simple utility/status card later

### Done When
- [ ] hand can be drawn
- [ ] attack card plays end-to-end
- [ ] defend card plays end-to-end
- [ ] card costs are enforced
- [ ] played cards leave hand correctly
- [ ] prototype attack/block buttons are no longer required

### Notes
This is the phase where the real combat loop begins to exist.

---

## PHASE 5 — ENEMY PARITY

### Objective
Make enemies use the same formal gameplay path as the player.

### What This Phase Must Establish
Enemy turns should generate formal actions, not apply direct effects.

The difference between player and enemy should be:
- who chooses the behavior
not
- how the result resolves

### Core Questions This Phase Answers
- how does enemy intent become actionable?
- how are enemy attacks generated?
- does the enemy use the same action queue?
- are enemy actions subject to the same post-resolution rules?

### Build Tasks
- [ ] Define enemy intent data
- [ ] Define intent selection/update path
- [ ] Display intent before execution
- [ ] On enemy turn:
  - [ ] read intent
  - [ ] generate corresponding actions
  - [ ] enqueue those actions
- [ ] Remove direct enemy damage logic
- [ ] Make enemy behavior follow same resolution rules as player
- [ ] Ensure death/battle over checks run the same way

### Done When
- [ ] enemy intent is visible
- [ ] enemy turn generates actions
- [ ] enemy actions resolve through same queue as player actions
- [ ] enemy no longer directly changes player state

### Notes
This phase is what gives the combat system symmetry.

---

## PHASE 6 — PERSISTENT COMBAT RULES

### Objective
Add systems that modify combat behavior across turns and actions.

### What This Phase Must Establish
Statuses and combat modifiers should plug into the action system, not bypass it.

### Core Questions This Phase Answers
- where are statuses stored?
- when do statuses tick?
- how do modifiers alter action resolution?
- what happens at start/end of turn?
- how do temporary combat rules persist across multiple turns?

### Build Tasks
- [ ] Define status representation
- [ ] Add status storage to actors
- [ ] Add start-of-turn hooks
- [ ] Add end-of-turn hooks
- [ ] Add modifier logic for actions:
  - [ ] strength-like
  - [ ] weak-like
  - [ ] vulnerable-like
  - [ ] dexterity-like if relevant
- [ ] Decide whether modifiers apply:
  - [ ] before enqueue
  - [ ] during resolution
  - [ ] or in a preprocess step
- [ ] Add status decrement / expiration rules
- [ ] Add status display/update

### Rules
- [ ] statuses should modify formal resolution
- [ ] statuses should not become random side paths for direct mutation
- [ ] start/end turn effects should belong to the battle flow, not scattered UI or actor scripts

### Done When
- [ ] statuses persist across turns
- [ ] statuses can affect actions predictably
- [ ] start/end turn effects resolve correctly
- [ ] no rule requires breaking the action pipeline

### Notes
Do not add lots of statuses immediately.
Add only enough to prove the architecture works.

---

## PHASE 7 — COMBAT COMPLETION

### Objective
Finish the single-encounter loop so combat can cleanly begin, run, and end.

### What This Phase Must Establish
A fight can fully complete and exit without leftover state corruption.

### Core Questions This Phase Answers
- what exactly ends combat?
- when is input locked?
- when does cleanup occur?
- what state survives after battle?
- how do you leave combat safely?

### Build Tasks
- [ ] Implement victory detection
- [ ] Implement defeat detection
- [ ] Lock combat input once battle outcome is final
- [ ] Clear or finalize action queue
- [ ] Run death / battle-end animations if desired
- [ ] Create cleanup path
- [ ] Remove/reset temporary combat state
- [ ] Add exit/transition path out of combat

### Done When
- [ ] combat starts cleanly
- [ ] combat ends cleanly
- [ ] no leftover state bleeds into the next encounter
- [ ] battle can safely transition to post-combat flow

---

## PHASE 8 — POST-REWRITE EXPANSION

### Objective
Only after the combat rewrite is stable, begin scaling the actual game.

### Add Later
- [ ] more cards
- [ ] more enemy patterns
- [ ] more statuses
- [ ] rewards
- [ ] deck growth
- [ ] relic systems
- [ ] map flow
- [ ] events
- [ ] shop/rest structure
- [ ] run persistence

### Rule
Do not treat content volume as progress before architecture is stable.

---

## CURRENT PRIORITY ORDER

Do these first, in order:

- [ ] create `obj_battle_controller`
- [ ] establish clean battle states
- [ ] move turn ownership into battle controller
- [ ] create action struct
- [ ] create action queue
- [ ] implement DAMAGE action
- [ ] implement GAIN_BLOCK action
- [ ] route current attack through queue
- [ ] route current block through queue
- [ ] route enemy attack through queue
- [ ] replace direct mutation paths
- [ ] only then begin card runtime flow

---

## HARD RULES

These are rewrite constraints, not suggestions.

- [ ] no direct HP mutation outside action resolution
- [ ] no direct block mutation outside action resolution
- [ ] UI is not allowed to own gameplay resolution
- [ ] battle controller owns battle flow
- [ ] battle controller owns queue lifecycle
- [ ] player and enemy must resolve through the same core path
- [ ] statuses must modify actions, not bypass the system
- [ ] prototype code may be referenced, but should not dictate final structure
- [ ] do not add lots of content before the pipeline is stable

---

## MILESTONES

### Milestone 1 — Clean Shell
- [ ] one controller owns battle
- [ ] one place controls turn progression
- [ ] battle states are explicit

### Milestone 2 — Formal Resolution
- [ ] damage and block go through action queue
- [ ] death checks happen in formal resolution path

### Milestone 3 — Prototype Bridge
- [ ] old buttons work only as request sources
- [ ] UI no longer directly mutates gameplay

### Milestone 4 — Real Card Loop
- [ ] draw hand
- [ ] play card
- [ ] validate cost
- [ ] generate actions
- [ ] resolve
- [ ] discard

### Milestone 5 — Enemy Parity
- [ ] enemy intent generates actions
- [ ] enemy and player share same resolution rules

### Milestone 6 — Stable Combat
- [ ] statuses work
- [ ] victory/defeat works
- [ ] encounter cleanup works

---

## PROGRESS

- [ ] Phase 1 — Battle Shell
- [ ] Phase 2 — Action Pipeline
- [ ] Phase 3 — Remove Direct Mutation
- [ ] Phase 4 — Card Play Loop
- [ ] Phase 5 — Enemy Parity
- [ ] Phase 6 — Persistent Combat Rules
- [ ] Phase 7 — Combat Completion
- [ ] Phase 8 — Post-Rewrite Expansion

Example completed task:
- [x] ~~Create obj_battle_controller~~

Use `- [x]` for completed tasks.
Use `~~text~~` if you also want strikethrough.