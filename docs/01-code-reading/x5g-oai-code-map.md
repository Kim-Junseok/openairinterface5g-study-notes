# X5G/OAI Code Map

Status: active  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`  
Related paths: `nfapi/oai_integration/`, `nfapi/oai_integration/aerial/`, `openair2/NR_PHY_INTERFACE/`, `openair2/LAYER2/NR_MAC_gNB/`, `openair2/LAYER2/nr_rlc/`, `openair2/LAYER2/nr_pdcp/`, `openair2/SDAP/nr_sdap/`

## Scope

This note is the first code-reading path for studying OAI without a physical testbed.

The goal is to map the X5G-relevant DU-high path:

```text
PHY/Aerial-side indication
  -> OAI FAPI/nFAPI boundary
  -> NR IF Module
  -> NR gNB MAC scheduler
  -> FAPI/nFAPI requests back toward PHY
```

This is a code-reading path first. Simulation and unit tests are supporting tools, not the starting point.

## What This Path Can Prove Without Testbed

| Claim | How to validate without RF hardware |
| --- | --- |
| OAI has a slot-indication entry path into DU-high scheduling | Read `nfapi/oai_integration/nfapi_vnf.c:phy_nr_slot_indication` |
| Scheduler output is represented by FAPI/nFAPI request structs | Read `openair2/NR_PHY_INTERFACE/NR_IF_Module.h:NR_Sched_Rsp_t` |
| The monolithic path calls the NR gNB MAC scheduler | Read `openair2/NR_PHY_INTERFACE/NR_IF_Module.c:run_scheduler_monolithic` |
| The per-slot scheduler orchestrates DL, UL, RA, SRS, and PUCCH scheduling | Read `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler.c:gNB_dlsch_ulsch_scheduler` |
| DL HARQ feedback state changes are visible in MAC code | Read `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_uci.c:find_harq` and `handle_dl_harq` |
| FAPI message encode/decode behavior can be checked through tests | Inspect `nfapi/tests/p7/` and `nfapi/tests/p5/` |
| PHY transport behavior can be explored through OAI physims | Inspect `openair1/SIMULATION/tests/CMakeLists.txt` |

## Diagrams

| Diagram | Purpose |
| --- | --- |
| [x5g-oai-boundary.puml](../../diagrams/architecture/x5g-oai-boundary.puml) | Compact component map for Aerial/OAI/L2 boundary |
| [slot-scheduling-path.puml](../../diagrams/sequence/slot-scheduling-path.puml) | Message sequence chart for slot scheduling |
| [dl-harq-feedback.puml](../../diagrams/call-flows/dl-harq-feedback.puml) | DL HARQ ACK/NACK state flow |

## Phase 1: Directory Map Only

Run these from the OAI source root:

```bash
rg --files openair2/LAYER2/NR_MAC_gNB
rg --files openair2/NR_PHY_INTERFACE nfapi/oai_integration/aerial
rg --files openair2/LAYER2/nr_rlc openair2/LAYER2/nr_pdcp openair2/SDAP/nr_sdap
```

Do not read all files yet. First memorize the ownership map:

| Area | Path | Why it matters |
| --- | --- | --- |
| NR gNB MAC | `openair2/LAYER2/NR_MAC_gNB/` | Scheduler, HARQ, PUCCH, RA |
| MAC-PHY interface | `openair2/NR_PHY_INTERFACE/` | Scheduler callbacks and response structs |
| FAPI/nFAPI | `nfapi/` | Message boundary between OAI and PHY-side implementations |
| Aerial integration | `nfapi/oai_integration/aerial/` | nvIPC/FAPI path relevant to Aerial-style split |
| NR RLC | `openair2/LAYER2/nr_rlc/` | Buffer/state below PDCP and above MAC-facing primitives |
| NR PDCP | `openair2/LAYER2/nr_pdcp/` | PDCP entity and security/data handling |
| SDAP | `openair2/SDAP/nr_sdap/` | QoS flow and DRB mapping context |

## Phase 2: Slot Scheduling Path

Search:

```bash
rg -n "phy_nr_slot_indication|NR_slot_indication|gNB_dlsch_ulsch_scheduler|NR_Sched_Rsp_t|oai_fapi_" \
  nfapi openair2/NR_PHY_INTERFACE openair2/LAYER2/NR_MAC_gNB
```

Read in this order:

| Order | Source anchor | Read for |
| --- | --- | --- |
| 1 | `nfapi/oai_integration/nfapi_vnf.c:phy_nr_slot_indication` | Slot indication entry and request send-back logic |
| 2 | `openair2/NR_PHY_INTERFACE/NR_IF_Module.h:NR_Sched_Rsp_t` | Scheduler output fields: `DL_req`, `UL_tti_req`, `UL_dci_req`, `TX_req` |
| 3 | `openair2/NR_PHY_INTERFACE/NR_IF_Module.c:run_scheduler_monolithic` | Bridge from slot indication to scheduler |
| 4 | `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler.c:gNB_dlsch_ulsch_scheduler` | Per-slot scheduling order |
| 5 | `nfapi/oai_integration/aerial/fapi_nvIPC.c:oai_fapi_*` | Aerial-side request wrapper functions |

## Phase 3: DL Scheduler Focus

Search:

```bash
rg -n "nr_schedule_ue_spec|nr_dlsch_preprocessor|available_dl_harq|retrans_dl_harq|nr_acknack_scheduling|get_mcs_from_bler|nr_find_nb_rb" \
  openair2/LAYER2/NR_MAC_gNB
```

Read:

- `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_dlsch.c`
- `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_dlsch_default_policies.c`
- `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_primitives.c`
- `openair2/LAYER2/NR_MAC_gNB/nr_mac_gNB.h`

Focus questions:

- Where is retransmission prioritized over new transmission?
- Where is a DL HARQ process selected?
- Where do MCS, TBS, and RB allocation appear?
- Where is ACK/NACK feedback reserved?
- Which state is per UE, per HARQ process, or per slot?

## Phase 4: HARQ And ACK/NACK

Search:

```bash
rg -n "handle_dl_harq|find_harq|nr_schedule_pucch|handle_nr_uci_pucch|retrans_dl_harq|feedback_dl_harq" \
  openair2/LAYER2/NR_MAC_gNB
```

Minimal model:

```text
DL scheduling
  -> reserve PUCCH ACK/NACK occasion
  -> move HARQ pid into feedback_dl_harq
  -> receive UCI indication
  -> ACK: finish HARQ
  -> NACK: move HARQ pid into retrans_dl_harq and increment round
  -> max round exceeded: abort HARQ
```

## Phase 5: Runnable Anchors To Explore Later

This code path itself is not easy to unit-test end-to-end without constructing a large scheduler context. Use these runnable anchors instead:

| Runnable anchor | Why it helps |
| --- | --- |
| `nfapi/tests/p7/` | Tests FAPI P7 message packing/unpacking around the same boundary vocabulary |
| `openair2/LAYER2/nr_rlc/tests/` | Existing L2 unit tests with `ctest` integration |
| `openair1/SIMULATION/tests/CMakeLists.txt` | OAI physim tests for PHY-side behavior without RF hardware |
| `cmake --preset tests` / `cmake --build --preset tests` | Existing unit test build path |

See [../03-unit-test-lab/README.md](../03-unit-test-lab/README.md) for the unit-test feasibility notes.

## Explanation Target

After this note, you should be able to say:

> I did not try to read the whole OAI codebase first. I started from the X5G-relevant boundary: FAPI/nFAPI slot indications enter OAI through the VNF path, the NR IF module invokes the NR gNB MAC scheduler, the scheduler fills `DL_TTI`, `UL_TTI`, `UL_DCI`, and `TX_DATA` request structs, and the Aerial nvIPC path can send those requests back toward PHY-side processing. I then connected that to L2 concepts such as HARQ, ACK/NACK, MCS/TBS/RB allocation, and scheduler-visible buffer state.

