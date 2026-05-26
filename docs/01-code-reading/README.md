# Code Reading

Status: active  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`  
Related paths: `nfapi/`, `openair2/NR_PHY_INTERFACE/`, `openair2/LAYER2/NR_MAC_gNB/`, `openair2/LAYER2/nr_rlc/`, `openair2/LAYER2/nr_pdcp/`, `openair2/SDAP/nr_sdap/`

## Goal

Use source-code reading as the first study mode because there is no RF testbed available yet.

The first useful outcome is not a running gNB. It is the ability to explain, from code anchors, how a slot indication reaches the OAI scheduler and how scheduler output is returned as FAPI/nFAPI requests.

## First Reading Path

Start with [x5g-oai-code-map.md](x5g-oai-code-map.md).

Then read in this order:

| Step | OAI paths | Question |
| --- | --- | --- |
| 1 | `nfapi/oai_integration/nfapi_vnf.c` | Where does a slot indication enter OAI? |
| 2 | `openair2/NR_PHY_INTERFACE/NR_IF_Module.h` | What does `NR_Sched_Rsp_t` contain? |
| 3 | `openair2/NR_PHY_INTERFACE/NR_IF_Module.c` | Which callback runs the scheduler? |
| 4 | `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler.c` | What does the per-slot scheduler orchestrate? |
| 5 | `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_dlsch.c` | How does DL scheduling relate to HARQ, MCS, TBS, and RB allocation? |
| 6 | `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_uci.c` | How does ACK/NACK feedback move HARQ state? |
| 7 | `openair2/LAYER2/nr_rlc/`, `openair2/LAYER2/nr_pdcp/`, `openair2/SDAP/nr_sdap/` | What L2 state is relevant to scheduling and throughput reasoning? |

## Reading Rules

- Do not read all files in a directory before writing notes.
- Start from a message, callback, struct, or scheduler function.
- Record exact source anchors.
- Draw a diagram only after the source anchors are known.
- Keep RLC/PDCP shallow until the scheduler path needs deeper L2 context.

## Output Format

Each code-reading note should include:

```text
Status:
Source baseline:
Related paths:
Question:
Confirmed anchors:
What I can explain now:
What is still unknown:
Next grep:
```

## Diagrams

Current diagrams:

- `diagrams/architecture/x5g-oai-boundary.puml`
- `diagrams/sequence/slot-scheduling-path.puml`
- `diagrams/call-flows/dl-harq-feedback.puml`

