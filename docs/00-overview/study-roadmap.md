# Study Roadmap

Status: draft  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`

## Phase 1: Orientation

- Read upstream `README.md`, `doc/README.md`, `doc/BUILD.md`, `doc/SW_archi.md`.
- Build a directory map and terminology glossary.
- Identify main binaries and minimum reproducible run commands.

Deliverables:

- `docs/00-overview/repository-map.md`
- `docs/99-references/glossary.md`
- `docs/01-build-and-run/build-notes.md`

## Phase 2: Build and Runtime Flow

- Trace CMake/build scripts from `cmake_targets/`.
- Document config loading from `common/config/`.
- Document logging/tracing path from `common/utils/LOG/` and `common/utils/T/`.
- Capture startup sequence for one gNB and one nrUE flow.

Deliverables:

- `docs/01-build-and-run/build-notes.md`
- `docs/01-build-and-run/runbook.md`
- `docs/02-architecture/runtime-infrastructure.md`

## Phase 3: RAN Stack Reading

- Start from `openair2/GNB_APP`, `openair2/LAYER2`, and `openair2/RRC/NR`.
- Follow interfaces down to `openair1/PHY` and up to `openair3/NGAP`.
- Add one call-flow note per procedure.

Deliverables:

- `docs/03-ran-stack/gnb-app.md`
- `docs/03-ran-stack/mac-scheduler.md`
- `docs/03-ran-stack/rrc.md`
- `diagrams/call-flows/`

## Phase 4: Experiments

- Reproduce a simulator-based setup first.
- Record commands, config files, logs, expected output, and failure modes.
- Separate verified facts from assumptions.

Deliverables:

- `docs/04-deployment/rf-simulator.md`
- `docs/06-experiments/`
- `notes/daily/`

