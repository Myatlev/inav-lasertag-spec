# inav-lasertag-spec

`inav-lasertag-spec` is the contract repository for the open-source airborne laser tag / IR combat platform built around iNav.

This repository defines how the ecosystem is expected to behave before implementation details diverge across codebases. It exists to keep `iNav`, `iNav Configurator`, and the external controller aligned around one shared MVP and one shared protocol.

## Purpose

This repository is the source of truth for:

- MVP scope and non-goals;
- gameplay model and state transitions;
- UART protocol between the FC and the external controller;
- reference hardware direction and constraints;
- validation criteria and test planning;
- future protocol and platform extensions.

## What this repository is not

This repository is not the place for:

- iNav firmware implementation;
- Configurator UI implementation;
- controller firmware implementation;
- target-specific build systems.

Those live in their own repositories and should follow the contracts defined here.

## Initial document set

The first version of the spec focuses on the minimum vertical slice required to prove the gameplay concept:

- `docs/mvp-v0.1.md`
- `docs/game-model-v0.1.md`
- `docs/uart-protocol-v0.1.md`
- `tests/acceptance-criteria-v0.1.md`

## Guiding principles

- fun before realism for the MVP;
- gameplay logic belongs in `iNav`;
- the external controller must remain simple and fast;
- the protocol must be easy to implement on hobby-friendly hardware;
- the platform should stay reproducible and community-extensible.

## Related repositories

- `inav-lasertag`: meta-repository / workspace
- `inav`: gameplay authority inside the flight controller
- `inav-configurator`: setup and UX layer
- `inav-lasertag-controller`: reference external controller firmware