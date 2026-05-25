# Deployment Notes

Status: draft  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`

## Target Scenarios

| Scenario | Upstream docs | Notes |
| --- | --- | --- |
| RF simulator | `doc/NR_SA_Tutorial_OAI_nrUE.md`, `doc/L1SIM.md` | Start here for repeatability |
| OAI CN5G SA | `doc/NR_SA_Tutorial_OAI_CN5G.md` | Needs core network setup |
| COTS UE SA | `doc/NR_SA_Tutorial_COTS_UE.md` | Needs radio and SIM details |
| O-RAN FHI 7.2 | `doc/ORAN_FHI7.2_Tutorial.md` | Split/fronthaul-specific |
| Containers and CI | `docker/`, `ci-scripts/` | Useful for reproducible labs |

## Record Per Scenario

- Hardware and network topology.
- OAI commit and config files.
- Exact command lines.
- Expected logs.
- Packet captures or traces.
- Failure modes and fixes.

