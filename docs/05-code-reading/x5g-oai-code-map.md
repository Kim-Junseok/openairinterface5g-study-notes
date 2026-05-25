# Day2 X5G/OAI Code Map

Status: draft  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`  
Related paths: `nfapi/oai_integration/`, `nfapi/oai_integration/aerial/`, `openair2/NR_PHY_INTERFACE/`, `openair2/LAYER2/NR_MAC_gNB/`, `openair2/LAYER2/nr_rlc/`, `openair2/LAYER2/nr_pdcp/`, `openair2/SDAP/nr_sdap/`

## Input Prompt

This note is based on `/home/jjuns/interview-prep/notes/Day2_OAI_Code_Map.md` and verified against the local OAI checkout.

The Day2 goal is not to understand all of OAI. The goal is to map the X5G-relevant DU-high path:

```text
Aerial/PHY indication
  -> OAI FAPI/nFAPI boundary
  -> NR IF Module
  -> NR gNB MAC scheduler
  -> FAPI requests back to PHY
```

## Diagrams

| Diagram | Purpose |
| --- | --- |
| [day2-x5g-oai-boundary.puml](../../diagrams/architecture/day2-x5g-oai-boundary.puml) | Component-level map between Aerial, nFAPI, NR IF, MAC, RLC/PDCP/SDAP |
| [day2-slot-scheduling-path.puml](../../diagrams/sequence/day2-slot-scheduling-path.puml) | Message sequence chart for slot scheduling |
| [day2-dl-harq-feedback.puml](../../diagrams/call-flows/day2-dl-harq-feedback.puml) | DL HARQ ACK/NACK activity flow |

## Confirmed Code Anchors

| Area | Source anchor | Role |
| --- | --- | --- |
| Aerial integration | `nfapi/oai_integration/aerial/fapi_nvIPC.c` | nvIPC-based Aerial/FAPI adapter |
| Aerial request wrappers | `nfapi/oai_integration/aerial/fapi_nvIPC.c:oai_fapi_dl_tti_req` | Sends `DL_TTI.request` back toward PHY |
| Aerial request wrappers | `nfapi/oai_integration/aerial/fapi_nvIPC.c:oai_fapi_ul_tti_req` | Sends `UL_TTI.request` back toward PHY |
| Aerial request wrappers | `nfapi/oai_integration/aerial/fapi_nvIPC.c:oai_fapi_ul_dci_req` | Sends `UL_DCI.request` back toward PHY |
| Aerial request wrappers | `nfapi/oai_integration/aerial/fapi_nvIPC.c:oai_fapi_tx_data_req` | Sends `TX_DATA.request` back toward PHY |
| VNF slot indication | `nfapi/oai_integration/nfapi_vnf.c:phy_nr_slot_indication` | Receives slot indication, calls the NR IF scheduler callback, then sends FAPI requests |
| Scheduler response | `openair2/NR_PHY_INTERFACE/NR_IF_Module.h:NR_Sched_Rsp_t` | Holds `DL_req`, `UL_tti_req`, `UL_dci_req`, and `TX_req` |
| NR IF callback | `openair2/NR_PHY_INTERFACE/NR_IF_Module.h:NR_slot_indication` | Callback used by the VNF slot path |
| Monolithic scheduler bridge | `openair2/NR_PHY_INTERFACE/NR_IF_Module.c:run_scheduler_monolithic` | Resets the response and calls `gNB_dlsch_ulsch_scheduler()` |
| Slot scheduler | `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler.c:gNB_dlsch_ulsch_scheduler` | Per-slot orchestration for MIB/SIB/RA/SRS/UL/DL/PUCCH scheduling |
| DL scheduler | `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_dlsch.c:nr_schedule_ue_spec` | UE-specific DL scheduling |
| DL preprocessing | `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_dlsch.c:nr_dlsch_preprocessor` | DL candidate and allocation preprocessing |
| ACK/NACK scheduling | `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_uci.c:nr_acknack_scheduling` | Reserves feedback resources for DL HARQ |
| HARQ feedback lookup | `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_uci.c:find_harq` | Finds expected DL HARQ feedback by timing |
| HARQ result handling | `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_uci.c:handle_dl_harq` | Finishes, aborts, or moves HARQ to retransmission |

## Slot Scheduling Path

The Day2 slot path is:

1. Aerial/PHY sends a slot indication through the FAPI/nFAPI path.
2. `phy_nr_slot_indication()` in `nfapi_vnf.c` receives the indication.
3. It calls `ifi->NR_slot_indication(ind, &sched_response)`.
4. In monolithic mode, `run_scheduler_monolithic()` resets `NR_Sched_Rsp_t` and calls `gNB_dlsch_ulsch_scheduler()`.
5. The MAC scheduler fills scheduler response fields:
   - `DL_req`
   - `UL_tti_req`
   - `UL_dci_req`
   - `TX_req`
6. With Aerial enabled, `phy_nr_slot_indication()` sends non-empty requests through `oai_fapi_*` wrappers.

This is the code-level version of the interview sentence: OAI DU-high and Aerial DU-low are connected through a FAPI/nFAPI boundary, with the NR gNB MAC scheduler producing the request set consumed by PHY.

## Per-Slot MAC Scheduler Order

In `gNB_dlsch_ulsch_scheduler()`, the Day2-relevant ordering is:

| Order | Function / action | Why it matters |
| --- | --- | --- |
| 1 | Clear scheduler state for the slot | Avoids carrying stale NFAPI/VRB data |
| 2 | `schedule_nr_mib()` / `schedule_nr_sib1()` | Broadcast/system information scheduling |
| 3 | `schedule_nr_prach()` | PRACH resources before later UL allocations |
| 4 | `nr_csirs_scheduling()` / `nr_csi_meas_reporting()` | CSI-related scheduling |
| 5 | `nr_schedule_srs()` | SRS scheduling |
| 6 | `nr_schedule_RA()` | Random access path |
| 7 | `nr_schedule_ulsch()` | UL grant/PUSCH scheduling |
| 8 | `nr_schedule_ue_spec()` | UE-specific DL scheduling |
| 9 | `nr_sr_reporting()` | Scheduling request handling |
| 10 | `nr_schedule_pucch()` | PUCCH resources for feedback/control |

## DL HARQ / ACK-NACK Focus

The Day2 HARQ model is:

```text
DL scheduling
  -> reserve ACK/NACK feedback
  -> track HARQ in feedback_dl_harq
  -> receive UCI PUCCH feedback
  -> ACK: finish HARQ
  -> NACK: move to retrans_dl_harq and increment round
  -> max round: abort
```

Confirmed source anchors:

| Concept | Source anchor |
| --- | --- |
| Feedback waiting list | `openair2/LAYER2/NR_MAC_gNB/nr_mac_gNB.h:feedback_dl_harq` |
| Retransmission list | `openair2/LAYER2/NR_MAC_gNB/nr_mac_gNB.h:retrans_dl_harq` |
| Feedback resource scheduling | `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_uci.c:nr_acknack_scheduling` |
| UCI format 0/1 feedback handling | `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_uci.c:handle_nr_uci_pucch_0_1` |
| UCI format 2/3/4 feedback handling | `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_uci.c:handle_nr_uci_pucch_2_3_4` |

## RLC/PDCP Boundary For Day2

For Day2, keep RLC/PDCP shallow. The useful framing is not the internal retransmission or ciphering algorithms first; it is how scheduler behavior connects to buffer state, HARQ feedback, and PHY indications.

| Layer | Initial scope |
| --- | --- |
| NR RLC | Locate AM/UM entities and the OAI API surface |
| NR PDCP | Locate entity and OAI API surface |
| SDAP | Locate QoS flow to DRB mapping area |
| MAC/RLC | Find where scheduler-visible buffer or SDU status enters MAC |

## Interview Framing

Use this answer shape:

> I focused on the X5G-relevant OAI path instead of trying to read the whole codebase. I mapped how slot indications enter through the nFAPI/VNF layer, how `NR_IF_Module` invokes the NR gNB MAC scheduler, how the scheduler fills `DL_TTI`, `UL_TTI`, `UL_DCI`, and `TX_DATA` requests, and how the Aerial nvIPC integration sends those requests back toward GPU-accelerated PHY. Then I connected that to my L2 background through DL scheduling, HARQ, ACK/NACK feedback, MCS/TBS/RB allocation, and buffer-driven throughput behavior.

## Next Notes

- `docs/03-ran-stack/mac-scheduler.md`
- `docs/03-ran-stack/fapi-aerial-boundary.md`
- `docs/03-ran-stack/dl-harq-acknack.md`
- `docs/03-ran-stack/rlc-pdcp-scheduler-interface.md`

