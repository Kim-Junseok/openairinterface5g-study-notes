# openairinterface5g-study-notes

This repository is a guided study companion for reading the OpenAirInterface 5G RAN codebase.

It is written for two audiences:

- People who are opening the OAI 5G source tree for the first time.
- People who want code-level notes, diagrams, and reading paths for selected OAI RAN topics such as NR gNB MAC scheduling, FAPI/nFAPI, Aerial integration, HARQ, RLC, PDCP, and SDAP.

This is not an official OpenAirInterface repository. Use the official OAI documentation as the source of truth for installation, build, run, and contribution rules. Use this repository as a reading guide and annotation layer.

## Quick Start

Recommended local layout:

```text
workspace/
├── openairinterface5g/
└── openairinterface5g-study-notes/
```

Clone the OAI RAN source:

```bash
git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git
cd openairinterface5g
git checkout develop
git rev-parse HEAD
```

Clone these study notes:

```bash
git clone https://github.com/Kim-Junseok/openairinterface5g-study-notes.git
cd openairinterface5g-study-notes
```

Record the OAI commit you are reading:

```bash
cd ../openairinterface5g
git rev-parse HEAD
```

Then write that commit into any new note under `Source baseline`.

## Why The OAI Commit Matters

OAI `develop` changes frequently. A note that says "this function does X" is only useful if it also says which source revision was checked.

Each note in this repository should include:

```text
Status: draft
Source baseline: openairinterface5g@<commit>
Related paths: <OAI paths>
```

The initial local baseline used when this repository was bootstrapped was:

```text
openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc
```

See [source-baseline.md](docs/99-references/source-baseline.md) for baseline tracking.

## How To Use This Repository

This repository is now organized around the assumption that you do not have RF hardware or a complete external testbed yet.

| Step | Read | Purpose |
| --- | --- | --- |
| 1 | [No-testbed study plan](docs/README.md) | Understand the current learning strategy |
| 2 | [Code Reading](docs/01-code-reading/README.md) | Start from source anchors instead of trying to run a full setup |
| 3 | [X5G/OAI Code Map](docs/01-code-reading/x5g-oai-code-map.md) | Follow the first concrete OAI path: FAPI/nFAPI to NR gNB MAC scheduler |
| 4 | [Simulation](docs/02-simulation/README.md) | Identify runnable OAI simulation paths that do not need RF hardware |
| 5 | [Unit Test Lab](docs/03-unit-test-lab/README.md) | Use existing `ctest` tests and evaluate temporary local tests |
| 6 | [OAI Reference Map](docs/99-references/oai-reference-map.md) | Keep official docs and source areas close |

Current diagrams:

| Diagram | Read |
| --- | --- |
| OAI/Aerial boundary | [x5g-oai-boundary.puml](diagrams/architecture/x5g-oai-boundary.puml) |
| Slot scheduling sequence | [slot-scheduling-path.puml](diagrams/sequence/slot-scheduling-path.puml) |
| DL HARQ feedback flow | [dl-harq-feedback.puml](diagrams/call-flows/dl-harq-feedback.puml) |

## Getting The OAI Source

For code reading, the anonymous GitLab clone is enough:

```bash
git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git
cd openairinterface5g
git checkout develop
```

OAI's own documentation describes `develop` as the recommended/default branch for recent 5G work. Keep your local checkout current when you want notes to reflect newer code:

```bash
git pull --ff-only origin develop
git rev-parse HEAD
```

Official reference:

- [OAI GET_SOURCES.md](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/GET_SOURCES.md)

## Building OAI

For reading-only work, you can start without building. For simulation and unit-test work, follow the official build docs first.

The current OAI documentation recommends direct CMake/Ninja builds for a default build:

```bash
cd openairinterface5g
cmake -B build -G Ninja
cmake --build build
```

OAI also supports the historical `build_oai` wrapper. It is useful for dependency installation and softmodem-specific builds:

```bash
cd openairinterface5g/cmake_targets
./build_oai -I --install-optional-packages -w SIMU
./build_oai --ninja --gNB --nrUE
```

The exact command depends on your target: unit tests, physims, RF simulator, USRP, gNB-only, nrUE, CU/DU split, Aerial, or other setups.

Official references:

- [OAI BUILD.md](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/BUILD.md)
- [OAI documentation overview](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/README.md)

## Reading Strategy

Do not try to read all of OAI linearly.

Use a narrow path:

1. Choose one runtime scenario or one subsystem.
2. Identify the relevant executable, callback, or protocol boundary.
3. Follow a small number of files.
4. Write down confirmed code anchors.
5. Draw the message sequence or state flow in PlantUML.
6. Move uncertain claims into `Hypotheses` until verified.

For each source file or module, capture:

- Purpose.
- Important structs and enums.
- Initialization path.
- Runtime callbacks.
- Messages exchanged with other layers.
- Logs or trace points.
- Open questions.

This repo intentionally keeps the document structure small. For now, prefer adding focused notes under `docs/01-code-reading/`, `docs/02-simulation/`, or `docs/03-unit-test-lab/` instead of creating broad placeholder documents.

## Diagram Policy

Use Markdown for prose and tables. Use PlantUML for diagrams.

| Diagram type | Location |
| --- | --- |
| Architecture / component diagrams | `diagrams/architecture/*.puml` |
| Message sequence charts | `diagrams/sequence/*.puml` |
| Call-flow, activity, or state diagrams | `diagrams/call-flows/*.puml` |

Component diagrams include:

```plantuml
!pragma layout smetana
```

This avoids a hard dependency on Graphviz `dot` for local preview. If your PlantUML setup supports Graphviz and you prefer its layout, you can remove that pragma in a local experiment.

## Repository Structure

```text
openairinterface5g-study-notes/
├── AGENTS.md
├── README.md
├── docs/
│   ├── README.md
│   ├── 01-code-reading/
│   ├── 02-simulation/
│   ├── 03-unit-test-lab/
│   └── 99-references/
├── diagrams/
│   ├── architecture/
│   ├── call-flows/
│   └── sequence/
├── assets/
│   └── images/
└── notes/
    └── daily/
```

## Suggested First Reading Paths

### General OAI Newcomer

1. `doc/GET_SOURCES.md`
2. `doc/README.md`
3. `doc/BUILD.md`
4. `README.md` in the OAI source root
5. [docs/README.md](docs/README.md)
6. [Code Reading](docs/01-code-reading/README.md)

### NR gNB MAC Scheduler

1. `openair2/LAYER2/NR_MAC_gNB/`
2. `openair2/NR_PHY_INTERFACE/`
3. `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler.c`
4. `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_dlsch.c`
5. `openair2/LAYER2/NR_MAC_gNB/gNB_scheduler_uci.c`

### FAPI/nFAPI And Aerial Boundary

1. `nfapi/oai_integration/nfapi_vnf.c`
2. `nfapi/oai_integration/aerial/fapi_nvIPC.c`
3. `openair2/NR_PHY_INTERFACE/NR_IF_Module.h`
4. `openair2/NR_PHY_INTERFACE/NR_IF_Module.c`
5. [x5g-oai-code-map.md](docs/01-code-reading/x5g-oai-code-map.md)

## Official OAI References

- OAI RAN repository: <https://gitlab.eurecom.fr/oai/openairinterface5g>
- Source download guide: <https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/GET_SOURCES.md>
- Build guide: <https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/BUILD.md>
- Documentation overview: <https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/README.md>
- Software architecture: <https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/SW_archi.md>
- NR nFAPI architecture: <https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/NR_NFAPI_archi.md>
- RF simulator tutorial: <https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/NR_SA_Tutorial_OAI_nrUE.md>
- OAI 5GC SA tutorial: <https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/NR_SA_Tutorial_OAI_CN5G.md>
- Aerial FAPI split tutorial: <https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/Aerial_FAPI_Split_Tutorial.md>
- OAI GitLab wiki: <https://gitlab.eurecom.fr/oai/openairinterface5g/-/wikis/home>

## Writing Rules

- Keep notes evidence-backed.
- Prefer exact file paths, function names, structs, and messages.
- Use `Verified` for checked facts and `Hypothesis` for unconfirmed reasoning.
- Avoid long source-code copies; point to source anchors instead.
- Keep diagrams small enough to preview comfortably.
- Update the source baseline when reading a newer OAI commit.

See [AGENTS.md](AGENTS.md) for the detailed authoring workflow.
