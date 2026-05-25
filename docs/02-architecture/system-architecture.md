# System Architecture

Status: draft  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`  
Related paths: `doc/SW_archi.md`, `doc/SW-archi-graph.md`, `openair1/`, `openair2/`, `openair3/`

## Working Model

OAI 5G can be read as a set of runtime entry points that connect:

- radio and simulation backends,
- PHY processing,
- MAC scheduling,
- RLC/PDCP/SDAP data paths,
- RRC control procedures,
- NGAP/F1AP/E1AP and other protocol interfaces,
- shared runtime utilities such as config, logging, tracing, and inter-task messaging.

## Layer-Oriented Map

| Layer / Area | Representative paths | Notes |
| --- | --- | --- |
| PHY | `openair1/PHY/`, `openair1/SCHED_NR/` | Signal processing, transport channels, references, estimates |
| MAC | `openair2/LAYER2/MAC/`, `openair2/LAYER2/NR_MAC_gNB/` | Scheduling, gNB MAC behavior, UE MAC pieces |
| RLC/PDCP/SDAP | `openair2/LAYER2/`, `openair2/SDAP/` | User-plane and bearer handling |
| RRC | `openair2/RRC/NR/`, `openair2/RRC/NR_UE/` | Radio resource control procedures |
| RAN application | `openair2/GNB_APP/`, `openair2/ENB_APP/` | Node-level app configuration and orchestration |
| Core-facing protocols | `openair3/NGAP/`, `openair3/NAS/`, `openair3/SCTP/` | AMF/NAS/SCTP-facing integration |
| Split interfaces | `openair2/F1AP/`, `openair2/E1AP/`, `openair2/E2AP/` | CU/DU, CU-CP/CU-UP, near-RT RIC integration |
| Runtime infrastructure | `common/` | config, logs, ITTI, tracing, threads |
| RF and transport | `radio/` | USRP, RF simulator, FHI 7.2, ZMQ |

## Diagram Backlog

- Startup sequence for gNB.
- Config loading flow.
- RRC setup flow.
- Downlink scheduling path.
- Uplink scheduling path.
- RF simulator path.

