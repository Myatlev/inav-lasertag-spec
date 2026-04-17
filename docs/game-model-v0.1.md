# Game Model v0.1

## Purpose

This document defines the gameplay behavior of the MVP independently of implementation details.

## Core philosophy

The MVP prioritizes:

- clarity;
- repeatability;
- fun gameplay;
- low implementation complexity.

It does not attempt to simulate realistic aircraft damage.

## Entities

### Aircraft

Each participating aircraft has:

- `player_id`
- gameplay `state`
- `deaths`
- `respawn_deadline_ms`

## States

### `ALIVE`

The aircraft:

- may fire;
- may receive hits;
- shows active gameplay state in OSD.

### `DESTROYED`

The aircraft:

- may not fire;
- is waiting for respawn;
- shows destroyed state and respawn timer in OSD.

## State transitions

### `ALIVE -> DESTROYED`

Trigger:

- a valid `HIT` event is accepted by `iNav`.

Effects:

- set state to `DESTROYED`;
- increment `deaths`;
- start respawn timer;
- show `HIT` and `DESTROYED` feedback.

### `DESTROYED -> ALIVE`

Trigger:

- respawn timer expires.

Effects:

- set state to `ALIVE`;
- clear destroyed-only UI state;
- show `RESPAWNED`.

## Fire rules

### Fire allowed

Fire is allowed only when:

- feature is enabled;
- aircraft state is `ALIVE`;
- controller is ready;
- fire cooldown deadline has passed;
- no implementation-specific safety gate blocks the command.

### Fire blocked

Fire must be blocked when:

- aircraft state is `DESTROYED`;
- feature is disabled;
- controller is not ready.

## Hit processing rules

### Accept hit

A `HIT` may be accepted when:

- aircraft state is `ALIVE`;
- the packet is structurally valid;
- the payload carries a valid shooter identifier;
- hit invulnerability window has expired;
- implementation-level debounce or validation conditions pass.

### Ignore hit

A `HIT` should be ignored when:

- aircraft state is `DESTROYED`;
- the packet is invalid or corrupted;
- the aircraft is still in hit invulnerability window;
- the event fails debounce or duplicate suppression rules.

## Local counters

### Deaths

- Increment `deaths` when an `ALIVE` aircraft transitions to `DESTROYED` due to an accepted hit.
- Reset `deaths` on `ARM` event (`DISARMED -> ARMED` transition).
- Do not reset `deaths` on `DISARM` so post-flight results remain visible after landing.

## Shooter identity in v0.1

Each accepted `HIT` may carry a `shooter_id`, but in v0.1 this identifier is used only as lightweight source metadata for debugging and future expansion.

It is not required to produce a globally reliable `kills` counter in the MVP because:

- there is no shared match network;
- there is no central authority reconciling events across aircraft;
- the first goal is to validate the core fun loop, not final score attribution.

Reliable kill attribution is deferred to a future version.

## Respawn model

The respawn model in v0.1 is deliberately simple:

- respawn occurs in the air;
- respawn is timer-based only;
- no location check is required;
- no base return is required.

## MVP tunable gameplay parameters

- `respawn_timeout_s`
- `fire_rate_rpm`
- `hit_invulnerability_ms` (default 1000 ms)

## Player ID model

- `player_id` is limited to the range `0..7` in v0.1;
- this reflects an 8-player upper bound that matches practical FPV video channel constraints for early tests;
- the protocol should be implemented so that expanding the identifier width later remains possible.

## Non-goals

The following are not part of the v0.1 game model:

- HP > 1;
- subsystem damage;
- repair;
- rearm;
- surrender;
- team rules;
- class-based weapons;
- damage zones;
- globally reliable kill attribution.
