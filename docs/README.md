# OAI Study Plan Without RF Testbed

Status: active  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`  
Related paths: `doc/`, `openair1/SIMULATION/`, `openair2/`, `nfapi/`, `tests/`

## Purpose

This documentation tree is organized for studying OpenAirInterface 5G without a physical RF testbed.

The plan is intentionally narrow:

1. Read code paths that matter for NR gNB DU-high, FAPI/nFAPI, scheduler behavior, and selected L2 interfaces.
2. Use OAI-provided simulations where they are available.
3. Use existing unit tests and small temporary test branches to validate local understanding.

This repository does not try to replace official OAI documentation. It adds a code-reading path and notes that connect source files, diagrams, and runnable checks.

## Folder Structure

```text
docs/
├── README.md
├── 00-local-environment/
│   ├── README.md
│   ├── ubuntu-package-baseline.md
│   └── vscode-cmake-intellisense.md
├── 01-code-reading/
│   ├── README.md
│   └── x5g-oai-code-map.md
├── 02-simulation/
│   └── README.md
├── 03-unit-test-lab/
│   └── README.md
└── 99-references/
    ├── oai-reference-map.md
    └── source-baseline.md
```

## Recommended Order

| Order | Area | Goal |
| --- | --- | --- |
| 1 | [Local Environment](00-local-environment/README.md) | Prepare editor support for source-level reading |
| 2 | [Code Reading](01-code-reading/README.md) | Build a reliable map of OAI source paths before trying to run anything |
| 3 | [Simulation](02-simulation/README.md) | Identify OAI-provided runnable paths that do not need RF hardware |
| 4 | [Unit Test Lab](03-unit-test-lab/README.md) | Use existing `ctest` tests and evaluate small temporary tests |
| 5 | [References](99-references/oai-reference-map.md) | Keep official docs and local baselines close |

## Current Study Bias

The current priority is code reading, especially:

- X5G/OAI boundary framing.
- Aerial/FAPI/nFAPI path.
- `NR_IF_Module` scheduler callback boundary.
- NR gNB MAC scheduler entry points.
- DL HARQ and ACK/NACK feedback handling.
- RLC/PDCP/SDAP only as much as needed to understand scheduler-visible state and L2 data flow.

## What Is Not The Priority Yet

- Full RF deployment with SDR hardware.
- Full OAI CN5G + gNB + COTS UE integration.
- PHY algorithm deep dive.
- ASN.1 generated code.
- Full CU/DU/RIC protocol coverage.

Those can become separate phases later.
