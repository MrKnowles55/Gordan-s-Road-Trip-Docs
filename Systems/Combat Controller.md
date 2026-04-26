### Purpose
- Owns combat state and phase transitions
- Coordinates the flow of combat from start to end

## Core Rule
- All phase transitions are controlled centrally

## State

phase
- current combat phase

player
- player reference

enemies
- list of active enemies (ordered)

combat_active
- boolean indicating combat is ongoing

## Phases

start_turn
draw
player_phase
player_end_turn
enemy_phase
end_of_round
victory
defeat

## Order of Operations

Combat loop:

1. start_turn
2. draw
3. player_phase
4. player_end_turn
5. enemy_phase
6. end_of_round
7. repeat until victory or defeat

## Phase Transition

- Each phase completes its defined logic
- When complete:
  - controller advances to next phase

Rules:

- Phase does not advance until [[Action Queue]] is empty
- Phase order is fixed
- No phase may be skipped unless explicitly defined

## Start Combat

1. Initialize player state
2. Initialize enemies
3. Assign enemy positions
4. Determine initial enemy intents
5. Set phase = start_turn

## End Combat

Victory:
- all enemies are not alive

Defeat:
- player is not alive

On end:
- combat_active = false
- clear [[Action Queue]]

## Interaction

- Calls phase logic:
  - [[Start Turn Phase]]
  - [[Draw Phase]]
  - [[Player Phase]]
  - [[Player End Turn]]
  - [[Enemy Phase]]
  - [[End of Round]]

- Uses [[Action Queue]] to gate progression
- Reads enemy and player state to determine victory/defeat

## Edge Cases

Enemy dies mid-phase:
- removed or marked inactive
- does not act later in phase

Player dies mid-phase:
- transition to defeat immediately or after queue resolves (define rule)

Empty enemy list:
- transition to victory

## Standardization Rules

- Controller does not execute gameplay effects
- Controller only manages phase order and state
- All effects must resolve through [[Action Queue]] before advancing

## Related
- [[Start Turn Phase]]
- [[Draw Phase]]
- [[Player Phase]]
- [[Player End Turn]]
- [[Enemy Phase]]
- [[End of Round]]
- [[Action Queue]]