# MVP v0.1

## Objective

The MVP exists to answer one question:

Can a simple, open, affordable airborne laser tag loop built around iNav feel fun enough in real flight to justify deeper development?

The MVP is intentionally not a realism simulator. It is a playable vertical slice.

## MVP scope

### Included

- one FC-integrated gameplay loop;
- one external UART controller implementation target;
- one numeric `player_id`;
- one fire action from pilot input;
- hit registration from the external controller;
- aircraft states:
  - `ALIVE`
  - `DESTROYED`
- respawn timer in flight;
- fire blocked while `DESTROYED`;
- local destruction tracking:
  - `deaths`
- basic OSD state and event feedback.

### Excluded

- ammo;
- multi-hit HP systems;
- surrender;
- repair and rearm;
- GPS respawn logic;
- damage zones;
- team modes;
- complex match orchestration;
- weapon classes or weapon profiles;
- advanced anti-cheat mechanisms.

## Required platform behavior

### Fire

- Pilot triggers `FIRE` through an AUX / mode / button input.
- `iNav` decides whether the aircraft is currently allowed to fire.
- If allowed, `iNav` sends a `FIRE` command to the external controller.

### Hit registration

- The external controller receives and validates an IR packet.
- The controller reports a `HIT` event to `iNav`.
- `iNav` decides whether that event changes aircraft state.

### Destroy / respawn loop

- When a valid hit is accepted while in `ALIVE`, the aircraft becomes `DESTROYED`.
- `deaths` increments.
- A respawn timer starts immediately.
- While `DESTROYED`, further fire commands are blocked.
- When the timer expires, the aircraft returns to `ALIVE`.

## Minimal data model

The MVP requires at least:

- `feature_enabled`
- `player_id`
- `state`
- `respawn_timeout_ms`
- `respawn_deadline_ms`
- `deaths`

## MVP UX requirements

The pilot must be able to tell, without ambiguity:

- whether the aircraft is alive or destroyed;
- whether a hit was received;
- how long remains until respawn;
- what local `player_id` is currently configured;
- own destruction state.

## OSD minimum

### Persistent elements

- local `player_id`
- state: `ALIVE` / `DESTROYED`
- local `deaths` counter
- respawn timer while destroyed

### Event messages

- `HIT`
- `DESTROYED`
- `RESPAWN IN X`
- `RESPAWNED`

## Hardware assumptions for MVP

- 940nm IR emitter approach
- modulated carrier
- UART-connected external controller
- one practical receiver arrangement suitable for outdoor testing
- no requirement for multi-sensor coverage in v0.1

## Player ID constraints for v0.1

- `player_id` is encoded as `u8` in the FC <-> controller UART protocol;
- only values `0..7` are valid in v0.1;
- the IR shot payload uses the same 3-bit player identifier;
- wider identifiers are explicitly deferred to future protocol revisions.

## Success criteria

The MVP is considered successful when:

- the full `FIRE -> HIT -> DESTROYED -> RESPAWN` loop works consistently;
- bench tests are repeatable;
- ground tests are usable outdoors;
- flight tests show the loop is understandable and fun enough to continue.
