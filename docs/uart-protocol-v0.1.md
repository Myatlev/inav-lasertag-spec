# UART Protocol v0.1

## Purpose

This document defines the first UART protocol between `iNav` and the external `inav-lasertag-controller`.

The protocol is intentionally minimal. The controller is a peripheral, not the gameplay authority.

## Design goals

- simple to implement on hobby microcontrollers;
- explicit versioning;
- low framing overhead;
- enough metadata for reliable MVP behavior;
- no gameplay state on the controller.

## Transport

- physical transport: UART
- default serial mode: `115200 8N1`
- byte-oriented framed packets

## Frame format

```text
SOF | VER | MSG_TYPE | LEN | PAYLOAD | CRC8
```

### Fields

- `SOF`: start-of-frame byte, fixed to `0xA5`
- `VER`: protocol version, `0x01` for v0.1
- `MSG_TYPE`: message identifier
- `LEN`: payload length in bytes
- `PAYLOAD`: message-specific content
- `CRC8`: checksum covering `VER`, `MSG_TYPE`, `LEN`, and `PAYLOAD`

## Encoding rules

- multi-byte integers are little-endian;
- `player_id` is encoded as `u8`;
- only `player_id` values `0..7` are valid in v0.1;
- the effective player identifier width in v0.1 is 3 bits;
- unknown message types must be ignored safely;
- bad CRC frames must be discarded.

Rationale:

- early FPV combat tests are unlikely to exceed 8 simultaneous pilots;
- a shorter identifier helps keep the IR payload compact;
- the protocol version field leaves room to widen the identifier later if needed.

## Message set

### FC -> Controller

#### `PING`

Purpose:

- link health check

Payload:

- none

#### `SET_PLAYER_ID`

Purpose:

- configure the numeric player identifier used for transmitted shots

Payload:

- `player_id_u8`

#### `FIRE`

Purpose:

- instruct the controller to emit one shot packet

Payload:

- `seq_u8`
- `flags_u8`

Notes:

- the controller does not decide whether fire is allowed;
- the controller simply attempts transmission when instructed.

### Controller -> FC

#### `READY`

Purpose:

- indicate controller presence and capability after boot or reset

Payload:

- `fw_major_u8`
- `fw_minor_u8`
- `capabilities_u16`

#### `HIT`

Purpose:

- report a validated incoming shot packet

Payload:

- `shooter_id_u8`
- `rx_quality_u8`
- `seq_u8`

Notes:

- `rx_quality_u8` is implementation-defined and may represent normalized signal confidence;
- `seq_u8` helps duplicate suppression and debugging.
- `shooter_id_u8` uses only values `0..7` in v0.1.

#### `DEBUG`

Purpose:

- optional development diagnostics

Payload:

- implementation-defined

Notes:

- should not be required for MVP gameplay operation.

## Message identifiers

The exact numeric values may be implemented as:

```text
0x01 PING
0x02 SET_PLAYER_ID
0x03 FIRE
0x80 READY
0x81 HIT
0xF0 DEBUG
```

## Expected boot sequence

1. controller powers up;
2. controller sends `READY`;
3. FC sends `SET_PLAYER_ID`;
4. FC may periodically send `PING`;
5. controller is considered operational after successful initialization.

## Expected fire sequence

1. pilot requests fire;
2. `iNav` allows fire;
3. `iNav` sends `FIRE(seq, flags)`;
4. controller transmits an encoded IR shot packet using the configured `player_id`.

## Expected hit sequence

1. controller receives an IR packet;
2. controller validates modulation, framing, and checksum;
3. controller emits `HIT(shooter_id, rx_quality, seq)`;
4. `iNav` decides whether the hit changes gameplay state.

## Out of scope for v0.1

The following must not live in the UART protocol as gameplay decisions:

- score state;
- kill attribution decisions;
- respawn state;
- HP values;
- aircraft destruction decisions;
- match orchestration;
- team logic.

## Future-compatible extension space

Possible future additions:

- hit zone metadata;
- richer diagnostics;
- explicit capability negotiation;
- optional hardware telemetry;
- alternate transport bindings.
