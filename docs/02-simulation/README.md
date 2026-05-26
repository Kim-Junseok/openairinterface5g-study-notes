# Simulation

Status: active  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`  
Related paths: `doc/physical-simulators.md`, `doc/NR_SA_Tutorial_OAI_nrUE.md`, `openair1/SIMULATION/`, `radio/rfsimulator/`, `ci-scripts/yaml_files/5g_rfsimulator/`

## Goal

Use OAI-provided simulation paths before attempting any RF hardware testbed.

There are two different simulation categories:

| Category | What it tests | Good for |
| --- | --- | --- |
| Physical simulators / physims | Standalone PHY channel or coding behavior | BLER/HARQ/PHY sanity checks, no gNB runtime needed |
| RF simulator / softmodem flows | gNB/nrUE runtime through an RF simulator device | Runtime integration, logs, config reading |

## Physim Path

OAI physims are standalone tests under:

```text
openair1/SIMULATION/NR_PHY/
openair1/SIMULATION/LTE_PHY/
openair1/SIMULATION/tests/CMakeLists.txt
```

Official OAI command pattern:

```bash
cd openairinterface5g
mkdir build
cd build
cmake .. -GNinja -DENABLE_PHYSIM_TESTS=ON
ninja tests
ctest
```

Alternative via `build_oai`:

```bash
cd openairinterface5g/cmake_targets
./build_oai --ninja --phy_simulators
cd ran_build/build
ctest
```

Useful discovery commands:

```bash
ctest -N
ctest --print-labels
ctest -R nr_dl -N -V
ctest -L nr_ulschsim -j 4
```

## RF Simulator Path

RF simulator is closer to a real gNB/nrUE runtime than physim tests. It is the likely next step after code reading if no SDR hardware is available.

Initial references:

- `doc/NR_SA_Tutorial_OAI_nrUE.md`
- `doc/NR_SA_Tutorial_OAI_multi_UE.md`
- `radio/rfsimulator/README.md`
- `ci-scripts/yaml_files/5g_rfsimulator/README.md`

Do not start here unless the code-reading map is already clear enough. RF simulator debugging can quickly become config-heavy.

## Recommended Study Order

1. Read `doc/physical-simulators.md`.
2. Build/list tests with `ENABLE_PHYSIM_TESTS=ON`.
3. Run a small `ctest -R` subset.
4. Inspect the test command with `ctest -N -V`.
5. Map the executable back to `openair1/SIMULATION/NR_PHY/`.
6. Only then move to RF simulator softmodem flows.

## Open Questions

- Which physim can best connect to the current X5G/OAI DU-high path?
- Which RF simulator tutorial is smallest for a first local run?
- Which logs reveal scheduler output without needing a full external testbed?

