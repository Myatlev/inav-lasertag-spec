# Acceptance Criteria v0.1

## Purpose

This document defines what must be true before the MVP can be considered functionally valid.

## MVP acceptance summary

The system passes v0.1 acceptance when it demonstrates a consistent end-to-end gameplay loop across bench, ground, and initial flight testing.

## Functional criteria

### Controller link

- the external controller announces itself to `iNav`;
- `iNav` can configure `player_id`;
- `iNav` can send `FIRE`;
- the controller can report `HIT`.

### Gameplay state

- aircraft starts in `ALIVE`;
- accepted hit while `ALIVE` changes state to `DESTROYED`;
- `deaths` increments on destruction;
- respawn timer starts immediately;
- timer expiry returns state to `ALIVE`;
- fire is blocked during `DESTROYED`.

### Local counters

- at least `deaths` is updated correctly;
- no globally reliable `kills` counter is required in v0.1.

### OSD

- OSD shows local `player_id`;
- OSD shows current state;
- OSD shows local destruction state;
- OSD shows respawn timer while destroyed;
- event messages appear for `HIT`, `DESTROYED`, and `RESPAWNED`.

## Bench acceptance

The system should pass all of the following on a bench setup:

- controller boots and announces `READY`;
- `SET_PLAYER_ID` is accepted and persisted for runtime use;
- a commanded `FIRE` causes one valid transmit action;
- a simulated or paired shot causes `HIT` delivery to `iNav`;
- duplicate or obviously invalid packets do not repeatedly destroy the aircraft.

## Ground acceptance

The system should pass all of the following outdoors on the ground:

- hit detection works in daylight test conditions;
- false positives remain low enough for gameplay testing;
- emitter direction is usable at practical hobby distances;
- the setup is mechanically mountable without unreasonable complexity.

## Flight acceptance

The system should pass all of the following in early flight trials:

- pilots can verify their own `player_id` from OSD before takeoff;
- pilots can understand aircraft state from OSD;
- the fire / hit / destroy / respawn loop works in the air;
- the system does not interfere with safe operation of the aircraft beyond the intended gameplay layer;
- the overall interaction is fun enough to justify the next iteration.

## Failure conditions

The MVP should be treated as not yet ready if any of the following dominate testing:

- frequent daylight false hits;
- repeated missed hits in otherwise valid engagement conditions;
- unstable controller-FC communication;
- unclear pilot feedback;
- gameplay loop feels too rare or too chaotic to be enjoyable.

## Notes

This acceptance set intentionally measures both technical correctness and gameplay usefulness. Passing a protocol test alone is not enough if the flight experience is not practical or fun.
