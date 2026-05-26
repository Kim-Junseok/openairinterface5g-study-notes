# Agent Workflow

This repository turns OpenAirInterface 5G code reading into evidence-backed study notes, interview prep summaries, and PlantUML diagrams.

## Companion Repositories

Use these local repositories as inputs:

| Repository | Local path | Role |
| --- | --- | --- |
| OAI source checkout | `/home/jjuns/ran-code/openairinterface5g` | Primary code evidence |
| Study notes | `/home/jjuns/ran-code/openairinterface5g-study-notes` | Output repository |
| Interview prep | `/home/jjuns/interview-prep` | Planning notes and interview framing |

Do not edit `openairinterface5g` or `interview-prep` unless the user explicitly asks. Treat them as read-only references for this repo.

## Source Priority

When writing notes:

1. Start from the user-provided study prompt or `interview-prep/notes/*.md`.
2. Verify every code-level claim against the local OAI checkout.
3. Prefer exact OAI paths, functions, structs, and message names over broad summaries.
4. Separate confirmed findings from hypotheses.
5. Record the OAI source baseline in every new document.

## Day2 Default Scope

For the Day2 X5G/OAI code map, follow this order:

1. Directory map only:
   - `openair2/LAYER2/NR_MAC_gNB/`
   - `openair2/NR_PHY_INTERFACE/`
   - `nfapi/oai_integration/aerial/`
   - `nfapi/oai_integration/`
   - `openair2/LAYER2/nr_rlc/`
   - `openair2/LAYER2/nr_pdcp/`
   - `openair2/SDAP/nr_sdap/`
2. Slot scheduling path:
   - `phy_nr_slot_indication()`
   - `NR_Sched_Rsp_t`
   - `NR_slot_indication`
   - `run_scheduler_monolithic()`
   - `gNB_dlsch_ulsch_scheduler()`
3. DL scheduler:
   - `nr_schedule_ue_spec()`
   - `nr_dlsch_preprocessor()`
   - `available_dl_harq`
   - `retrans_dl_harq`
   - `nr_acknack_scheduling()`
   - `nr_find_nb_rb()`
4. HARQ and ACK/NACK:
   - `nr_schedule_pucch()`
   - `find_harq()`
   - `handle_dl_harq()`
   - `feedback_dl_harq`
   - `retrans_dl_harq`
5. FAPI/Aerial boundary:
   - `fapi_nvIPC.c`
   - `oai_fapi_dl_tti_req()`
   - `oai_fapi_ul_tti_req()`
   - `oai_fapi_ul_dci_req()`
   - `oai_fapi_tx_data_req()`

Skip ASN.1 generated code, LTE legacy paths, PHY internals, and broad E1/F1/E2 deep dives unless the user changes the scope.

## Output Rules

Use Markdown for prose, tables, and evidence notes. Use PlantUML for message sequence charts, component diagrams, activity diagrams, and state flows.

Place files as follows:

| Artifact | Location |
| --- | --- |
| Code-reading notes | `docs/01-code-reading/` |
| Simulation notes | `docs/02-simulation/` |
| Unit-test notes | `docs/03-unit-test-lab/` |
| Reference notes | `docs/99-references/` |
| Architecture diagrams | `diagrams/architecture/*.puml` |
| Message sequence charts | `diagrams/sequence/*.puml` |
| Call-flow or state diagrams | `diagrams/call-flows/*.puml` |

## Evidence Style

Use this evidence format in Markdown tables:

```text
`path/to/file.c:function_or_symbol`
```

When a line number is useful, use:

```text
`path/to/file.c:123`
```

Avoid copying long source snippets. Summarize behavior and point to the source path.

## PlantUML Style

- Use `.puml` extension.
- Start every diagram with `@startuml` and a clear `title`.
- Keep participant and component names stable across diagrams:
  - `Aerial PHY`
  - `Aerial nvIPC`
  - `OAI nFAPI VNF`
  - `NR IF Module`
  - `NR gNB MAC Scheduler`
  - `NR UE Scheduler Control`
- Put source anchors in PlantUML comments near the relevant step.
- Prefer concise labels that name the actual FAPI messages or OAI functions.

## Review Checklist

Before committing:

- No unsupported code claims.
- Every note has `Status`, `Source baseline`, and `Related paths`.
- Markdown links and relative paths resolve logically.
- PlantUML files are syntactically simple and readable.
- A Hangul text scan returns no Korean text unless the user asks for Korean output.
