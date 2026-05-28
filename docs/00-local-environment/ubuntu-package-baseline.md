# Ubuntu Package Baseline

Status: active  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`  
Related paths: `CMakeLists.txt`, `cmake_targets/tools/MODULES/Findsctp.cmake`, `nfapi/open-nFAPI/README.md`, `openair1/PHY/`, `openair2/COMMON/`, `openair3/SCTP/`

## Purpose

This note records host-level Ubuntu packages added while preparing a local OAI reading and build environment.

It is not a replacement for the official OAI dependency installer or build guide. Treat it as a recovery note for the local study setup.

## Current Host Snapshot

| Field | Value |
| --- | --- |
| OS | Ubuntu 24.04.4 LTS |
| Architecture | `x86_64` |
| Kernel context | WSL2, `6.6.87.2-microsoft-standard-WSL2` |
| Package source checked | `/var/log/apt/history.log`, `/var/log/dpkg.log`, `dpkg-query -W` |
| Last package update recorded here | 2026-05-29 |

## Packages Added On 2026-05-29

Manual installs:

```bash
sudo apt install libsctp-dev
sudo apt install autoconf
sudo apt install libsimde-dev libtool
```

Installed package versions observed after the install:

| Package | Version | Why it matters for OAI study/build |
| --- | --- | --- |
| `libsctp-dev` | `1.0.19+dfsg-2build1` | Provides SCTP headers and development files. OAI's top-level CMake requires `find_package(sctp REQUIRED)`, and several RAN control-plane headers include `netinet/sctp.h`. |
| `libsctp1` | `1.0.19+dfsg-2build1` | Runtime library installed with `libsctp-dev`. |
| `autoconf` | `2.71-3` | Required by autotools-based dependency flows such as `nfapi/open-nFAPI/README.md`. |
| `automake` | `1:1.16.5-1.3ubuntu1` | Installed with `autoconf`; commonly paired with autotools build regeneration. |
| `autotools-dev` | `20220109.1` | Installed with `autoconf`; provides helper files for autotools packages. |
| `m4` | `1.4.19-4build1` | Macro processor used by Autoconf. |
| `libsimde-dev` | `0.7.2-6` | Provides SIMDe headers used by OAI PHY and utility code, including files under `openair1/PHY/` and `common/utils/`. |
| `libtool` | `2.4.7-7build1` | Required by autotools-based dependency flows such as open-nFAPI. |
| `libltdl-dev` | `2.4.7-7build1` | Development package installed with `libtool`. |
| `libltdl7` | `2.4.7-7build1` | Runtime library installed with `libtool`. |

## Source Anchors

| Area | Evidence |
| --- | --- |
| SCTP CMake dependency | `CMakeLists.txt:1438`, `cmake_targets/tools/MODULES/Findsctp.cmake` |
| SCTP headers in OAI code | `openair2/COMMON/f1ap_messages_types.h`, `openair2/COMMON/ngap_messages_types.h`, `openair3/SCTP/sctp_common.c` |
| SIMDe compiler flags | `CMakeLists.txt:142`, `CMakeLists.txt:168` |
| SIMDe headers in OAI code | `openair1/PHY/sse_intrin.h`, `openair1/PHY/MODULATION/nr_modulation.c`, `common/utils/bits.c` |
| open-nFAPI autotools hints | `nfapi/open-nFAPI/README.md:46-53` |

## Maintenance Notes

When a configure or IntelliSense run fails after moving to a new machine, check missing headers before changing OAI source:

```bash
dpkg-query -W libsctp-dev libsimde-dev autoconf automake libtool
```

If the package is missing, install it through the OS package manager first. Keep local workarounds out of committed notes unless they are reproducible and not machine-specific.
