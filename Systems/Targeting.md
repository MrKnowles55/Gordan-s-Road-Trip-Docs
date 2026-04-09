==============================================================
TARGETING


DEFINITION
- Formal rules for determining required targets, validating targets, and resolving target sets for a card

==============================================================
TARGETING TYPES
==============================================================

none
- No target required
- Target set = null

self
- Target set = [source]

single_enemy
- Requires exactly one living enemy
- Target set = [selected enemy]

all_enemies
- Target set = all living enemies at time of validation

==============================================================
TARGET SET
==============================================================

- A resolved list of entities produced during card play validation
- Passed to effect generation and then to [[Action]] creation

Properties:
- Immutable after validation
- Used for all effects of that card play
- May contain 0..N entities depending on type

==============================================================
VALIDATION
==============================================================

Occurs during card play before cost is paid.

Validation checks:

- targeting type requirements are satisfied
- for single_enemy:
    - exactly one valid living enemy is selected
- for all_enemies:
    - at least one valid enemy exists (or define allow-empty behavior)

If validation fails:
- card cannot be played

==============================================================
RESOLUTION
==============================================================

- Target set is resolved once during validation
- Effects generate actions using this target set

Examples:

single_enemy damage:
- generate 1 damage action using target[0]

all_enemies damage:
- generate N damage actions (one per target)

==============================================================
VALID TARGET CRITERIA
==============================================================

An entity is valid if:

- exists in combat state
- is alive (unless effect explicitly allows dead targets)
- matches targeting type

==============================================================
TIMING INTERACTION
==============================================================

- Targeting resolves before any [[Action]] is enqueued
- Actions do not perform target selection
- Actions may revalidate target at resolution (for fizzle handling)

==============================================================
EDGE CASES
==============================================================

No valid enemies:

- single_enemy → cannot play
- all_enemies → define behavior:
    - recommended: cannot play

Target becomes invalid after validation:

- Action validation step determines outcome
- If invalid → action fizzles

Dynamic board changes during resolution:

- Target set does not change
- Each action validates its target independently

==============================================================
DESIGN CONSTRAINTS
==============================================================

- Targeting is declarative on [[Card]]
- Targeting produces a concrete target set before action generation
- No mid-resolution retargeting

==============================================================
RELATED
==============================================================

[[Card]]
[[Action]]
[[Action Queue]]