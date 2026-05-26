# Unit Test Lab

Status: active  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`  
Related paths: `doc/UnitTests.md`, `CMakePresets.json`, `nfapi/tests/`, `openair2/LAYER2/nr_rlc/tests/`, `openair2/LAYER2/nr_pdcp/tests/`, `openair1/PHY/*/tests/`, `openair1/SIMULATION/tests/`

## Feasibility Summary

Yes, unit-test-based study is possible, but the scope matters.

| Target | Feasibility | Notes |
| --- | --- | --- |
| Existing OAI unit tests | High | OAI already uses `ctest` and `ENABLE_TESTS` |
| Existing FAPI tests | High | `nfapi/tests/p5/` and `nfapi/tests/p7/` are useful for boundary concepts |
| Existing RLC/PDCP tests | High | Good L2 starting points without RF hardware |
| Existing physim tests | High | Use `ENABLE_PHYSIM_TESTS` for PHY simulation tests |
| New tiny utility tests | Medium | Feasible if the target function has limited global state |
| New MAC scheduler tests | Low-to-medium | Harder because scheduler code depends on large global/context state such as `RC`, UE context, config, NFAPI structs, and timing |
| End-to-end gNB scheduler unit test | Low | Better approached via simulation or a carefully built fixture, not as the first test |

## Existing Unit Test Commands

OAI supports CMake presets:

```bash
cd openairinterface5g
cmake --preset tests
cmake --build --preset tests
cd cmake_targets/ran_build/build_test
ctest -N
ctest --output-on-failure
```

Equivalent explicit CMake path:

```bash
cd openairinterface5g
cmake -B build_test -G Ninja -DENABLE_TESTS=ON
cmake --build build_test --target tests
cd build_test
ctest -N
ctest --output-on-failure
```

Run subsets:

```bash
ctest -R nr_rlc_tests --output-on-failure
ctest -R nr_fapi --output-on-failure
ctest -R test_nr_frame_params --output-on-failure
```

## Useful Existing Test Areas

| Area | Path | Why it is useful |
| --- | --- | --- |
| FAPI P5/P7 messages | `nfapi/tests/` | Boundary vocabulary: slot, DL_TTI, UL_TTI, UL_DCI, TX_DATA, CRC, UCI, RX_DATA |
| NR RLC | `openair2/LAYER2/nr_rlc/tests/` | Mature L2 unit-test example with stimuli and expected output |
| NR PDCP | `openair2/LAYER2/nr_pdcp/tests/` | Security-related PDCP test surface |
| NR MAC UE | `openair2/LAYER2/NR_MAC_UE/tests/` | Smaller MAC-adjacent procedures than gNB scheduler |
| PHY tools | `openair1/PHY/TOOLS/tests/` | Smaller utility-style tests and benchmarks |
| PHY init/transport/modulation | `openair1/PHY/INIT/tests/`, `openair1/PHY/NR_TRANSPORT/tests/`, `openair1/PHY/MODULATION/tests/` | Lower-level PHY unit tests |
| Physim | `openair1/SIMULATION/tests/` | Simulation tests registered through `ctest` |

## Temporary Test Strategy

If a new local test is needed, do not start by editing the scheduler core.

Recommended order:

1. Find an existing test in the same subsystem.
2. Build it with `ENABLE_TESTS=ON`.
3. Run it with `ctest -R <name> -V`.
4. Copy the pattern in a local OAI topic branch.
5. Guard the new test with `if(ENABLE_TESTS)`.
6. Keep the test small and delete it if it was only a learning scaffold.

For temporary work inside OAI, use a local branch such as:

```bash
cd openairinterface5g
git checkout -b study/local-unit-test-lab
```

Do not commit temporary test scaffolding to this notes repository. This repo should describe the test and link to source anchors; the runnable test belongs in the OAI checkout or a separate patch.

## First Unit-Test Reading Assignment

Start with FAPI and RLC because they connect to the current code-reading map without requiring a full gNB scheduler fixture:

1. `nfapi/tests/p7/nr_fapi_slot_indication_test.c`
2. `nfapi/tests/p7/nr_fapi_dl_tti_request_test.c`
3. `nfapi/tests/p7/nr_fapi_ul_tti_request_test.c`
4. `openair2/LAYER2/nr_rlc/tests/CMakeLists.txt`
5. `openair2/LAYER2/nr_rlc/tests/test_nr_rlc_am_entity.cpp`

## What To Avoid First

- Directly unit-testing `gNB_dlsch_ulsch_scheduler()` without understanding its required context.
- Creating tests that depend on wall-clock timing or live RF.
- Editing OAI `develop` directly.
- Writing a test before identifying the exact function or struct you want to learn.

