# RAN Stack Index

Status: draft  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`

## Pages to Fill

| Page | Primary paths | Priority |
| --- | --- | --- |
| `gnb-app.md` | `openair2/GNB_APP/` | high |
| `nr-ue.md` | `openair2/RRC/NR_UE/`, `openair1/PHY/NR_UE_*` | high |
| `mac-scheduler.md` | `openair2/LAYER2/NR_MAC_gNB/`, `openair1/SCHED_NR/` | high |
| `rlc-pdcp-sdap.md` | `openair2/LAYER2/`, `openair2/SDAP/` | medium |
| `rrc.md` | `openair2/RRC/NR/`, `doc/RRC/` | high |
| `ngap-nas.md` | `openair3/NGAP/`, `openair3/NAS/` | medium |
| `f1-e1-e2.md` | `openair2/F1AP/`, `openair2/E1AP/`, `openair2/E2AP/` | medium |
| `phy.md` | `openair1/PHY/`, `openair1/SCHED_NR/` | high |

## Per-Module Checklist

- Ownership and purpose.
- Important structs and enums.
- Initialization path.
- Main runtime callbacks.
- Messages exchanged with other layers.
- Logs/traces to enable during debugging.
- Known upstream docs.

