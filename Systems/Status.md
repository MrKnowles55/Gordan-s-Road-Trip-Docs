### Purpose
- Represents a single instance of a status applied to an entity

## Core Rule
- A status instance contains only data; behavior is defined in [[Status Effects]]

## Data

type
- status identifier (e.g. weak, vulnerable, strength)

owner
- entity the status is applied to

stacks
- numeric value representing duration or magnitude

source
- entity or system that applied the status

category
- must match a category defined in [[Status Effects]]

timing_hooks
- when this status is checked or updated

stacking_rule
- how this status combines with new applications

expiration_rule
- when this status is removed

active
- whether the status is currently active

data
- optional status-specific values

## Rules

- A status belongs to exactly one owner
- A status must reference a valid definition
- A status must define stacking and expiration behavior
- Status instances do not execute logic

## Lifecycle

1. Status is created and applied to an entity
2. Status is stored on the owner
3. Systems read the status at relevant timing hooks
4. Stacks may change based on rules
5. Status expires and is removed

## Interaction

- Read during:
  - [[Action]] resolution
  - phase timing hooks
  - card play

- Modified by:
  - new status applications
  - expiration rules

## Edge Cases

Duplicate application:
- resolved using stacking_rule

Stacks reach 0:
- removed at defined expiration timing

Owner removed from combat:
- status is removed unless otherwise defined

## Standardization Rules

- Status instance must contain all required fields
- No behavior is embedded in the instance
- All execution logic is external

## Related
- [[Status Effects]]
- [[Action]]