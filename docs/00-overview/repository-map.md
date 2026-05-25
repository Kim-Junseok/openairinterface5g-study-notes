# Repository Map

Status: draft  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`  
Related paths: `openair1/`, `openair2/`, `openair3/`, `common/`, `radio/`, `targets/`, `executables/`, `doc/`

## Top-Level View

| Path | Working meaning | Notes to expand |
| --- | --- | --- |
| `openair1/` | PHY, scheduler-facing PHY pieces, simulations | NR/LTE PHY, transport, reference signals, estimators |
| `openair2/` | Layer 2 and RAN application protocols | MAC, RLC, PDCP, SDAP, RRC, F1AP, E1AP, E2AP, gNB app |
| `openair3/` | NAS and core-network-facing protocol pieces | NGAP, S1AP, NAS, SCTP, GTP-U related code |
| `common/` | Shared runtime utilities and infrastructure | config, logging, tracing, ITTI, thread pool, telnet/web servers |
| `radio/` | RF device and radio abstraction support | USRP, BladeRF, RF simulator, FHI 7.2, ZMQ |
| `executables/` | Main binaries and executable entry points | gNB, nrUE, softmodem entry points |
| `targets/` | Target projects, configs, test assets | deployment/test project material |
| `ci-scripts/` | CI, test scenarios, config sets | reusable build/test scenario references |
| `cmake_targets/` | Build orchestration | build scripts and CMake integration |
| `doc/` | Upstream documentation | architecture, build, run, tutorials |

## Reading Heuristic

For each area, capture:

- What problem this directory owns.
- Main entry points and initialization flow.
- Public structs, callbacks, and message types.
- Threads/tasks involved.
- Logs/traces that reveal runtime behavior.
- Upstream docs that already explain part of the behavior.

## Open Questions

- Which binary should be used as the primary walkthrough target: `nr-softmodem`, `nr-uesoftmodem`, or simulator-first flows?
- Which deployment mode should be documented first: RF simulator SA, COTS UE SA, or O-RAN FHI 7.2?
- Should LTE paths be treated as historical context or first-class reading material?

