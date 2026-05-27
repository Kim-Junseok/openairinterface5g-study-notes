# VS Code CMake IntelliSense Setup

Status: active  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`  
Related paths: `CMakePresets.json`, `CMakeUserPresets.json`, `.vscode/settings.json`, `cmake_targets/`, generated ASN.1 headers

## Purpose

This note explains why VS Code may show many C/C++ red squiggles when opening the OAI source tree, and how to prepare a local CMake-based IntelliSense setup for code reading.

This is a reading-oriented setup. It is not a substitute for the official OAI build and run instructions.

## Why Red Squiggles Appear

OAI source files are not self-contained. A single `.c` file often depends on information that is produced by the build system:

- include directories selected by CMake
- compile definitions such as feature flags and protocol versions
- generated headers from ASN.1 specifications
- generated tracing headers
- third-party dependency include paths

If the editor opens a source file without that build context, it may report errors even when the source is valid in the real build.

## Core Concepts

| Term | Meaning |
| --- | --- |
| `CMakeLists.txt` | The build description read by CMake. |
| Configure step | The step where CMake reads the project and prepares a build directory. |
| Generator | The backend build system CMake writes, such as Ninja or Unix Makefiles. |
| Preset | A named configure or build option set stored in `CMakePresets.json` or `CMakeUserPresets.json`. |
| Build directory | A separate directory where generated files, cache files, and object files are written. |
| `compile_commands.json` | A compilation database that tells tools how each source file is compiled. |

For editor support, `compile_commands.json` is the key artifact. It gives the C/C++ extension the same include paths, definitions, and compiler flags that CMake computed.

## Recommended Local Pattern

Use a local user preset instead of editing OAI's tracked build files.

Recommended files:

```text
openairinterface5g/
├── CMakeUserPresets.json
└── .vscode/
    └── settings.json
```

Recommended build output:

```text
openairinterface5g/
└── cmake_targets/
    └── ran_build/
        └── vscode-intellisense/
            └── compile_commands.json
```

Keep these files local unless the team intentionally wants to share editor settings. Personal paths, caches, and generated build outputs should not be committed.

## High-Level Setup Flow

1. Install the VS Code C/C++ extension and CMake Tools extension.
2. Create a local CMake user preset for editor-oriented configuration.
3. Enable `CMAKE_EXPORT_COMPILE_COMMANDS`.
4. Run CMake configure with that preset.
5. Generate the OAI headers that are normally produced during the build.
6. Point the C/C++ extension to CMake Tools or to the generated `compile_commands.json`.
7. Reload VS Code or reset the IntelliSense database if stale diagnostics remain.

## Example User Preset Shape

The exact values depend on the host environment. The important parts are the local build directory and `CMAKE_EXPORT_COMPILE_COMMANDS`.

```json
{
  "version": 3,
  "configurePresets": [
    {
      "name": "vscode-intellisense",
      "displayName": "VS Code IntelliSense",
      "generator": "Unix Makefiles",
      "binaryDir": "${sourceDir}/cmake_targets/ran_build/vscode-intellisense",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "RelWithDebInfo",
        "CMAKE_EXPORT_COMPILE_COMMANDS": "ON"
      }
    }
  ]
}
```

If Ninja is installed and preferred, use the generator that matches the local environment.

## Generated Headers

Some OAI headers are generated during the build. For code reading, it is often enough to build generation-related targets before attempting full binaries.

Useful target families include:

| Target family | Why it matters |
| --- | --- |
| `generate_T` | Generates trace-related headers. |
| `gen_nr_rrc_hdrs` | Generates NR RRC ASN.1 headers. |
| `gen_lte_rrc_hdrs` | Generates LTE RRC ASN.1 headers. |
| `asn1_f1ap`, `asn1_ngap`, `asn1_e1ap` | Generates and builds key 5G RAN protocol ASN.1 outputs. |
| `asn1_x2ap`, `asn1_s1ap`, `asn1_xnap` | Generates and builds additional protocol ASN.1 outputs used by related modules. |

Do not treat generated ASN.1 code as the first reading target. For most study paths, it is supporting material that helps the editor resolve includes.

## What This Setup Solves

This setup should improve:

- missing include diagnostics
- unknown macro diagnostics
- unknown generated type diagnostics
- navigation from source files to generated protocol headers
- consistency between editor analysis and CMake build configuration

It does not guarantee that the full OAI runtime stack is ready. Running gNB, nrUE, RF simulator, or hardware-backed scenarios still requires the official dependency and runtime setup.

## Public Documentation Boundary

It is useful to document the general approach publicly because many first-time OAI readers hit the same editor problem.

Avoid publishing:

- personal absolute paths
- machine-specific package extraction workarounds
- local cache paths
- temporary sysroot or environment-variable hacks
- full terminal logs

Keep those details in private notes if they are useful for local recovery.

