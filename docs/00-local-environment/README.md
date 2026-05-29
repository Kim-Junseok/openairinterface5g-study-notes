# Local Environment

Status: active  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`  
Related paths: `CMakePresets.json`, `CMakeUserPresets.json`, `.vscode/settings.json`, `cmake_targets/ran_build/`, `openairinterface5g-study.code-workspace`

This is the local setup checklist for reading and building OpenAirInterface 5G with host Ubuntu packages, CMake presets, Ninja, and VS Code IntelliSense.

It intentionally avoids the old `cmake_targets/ran_build/local-sysroot/` workaround.

## 1. Host Apt Packages

Install host packages first:

```bash
sudo apt-get install -y \
  build-essential cmake ninja-build git pkg-config \
  autoconf automake autotools-dev libtool m4 bison flex libfl-dev \
  libsctp-dev libsimde-dev libconfig-dev libssl-dev libyaml-cpp-dev \
  zlib1g-dev libreadline-dev libncurses-dev libpcre2-dev
```

Package checklist:

| Package | Needed for |
| --- | --- |
| `build-essential` | Provides the normal host compiler/build base: `gcc`, `g++`, `make`, headers, and libc development files. |
| `cmake` | Runs OAI's top-level `CMakeLists.txt` and preset-based configure/build flow. |
| `ninja-build` | Matches OAI's tracked `default` CMake preset generator. |
| `git` | Required by OAI's CMake flow when it fetches managed dependencies such as the ASN.1 compiler. |
| `pkg-config` | Lets CMake discover host libraries such as `libconfig`, OpenSSL, and other development packages. |
| `autoconf` | Required to configure autotools-based components such as the OAI-managed ASN.1 compiler build. |
| `automake` | Required with Autoconf for autotools-generated Makefile flows. |
| `autotools-dev` | Provides helper files used by autotools projects. |
| `libtool` | Required by autotools projects that build/install libraries and tools. |
| `m4` | Macro processor used by Autoconf. |
| `bison` | Parser generator required by ASN.1 compiler build tooling. |
| `flex` | Lexer generator required by ASN.1 compiler build tooling. |
| `libfl-dev` | Provides the Flex support library used by lexer-based tools. |
| `libsctp-dev` | Provides SCTP headers/libraries; OAI's CMake requires SCTP and RAN control-plane code includes `netinet/sctp.h`. |
| `libsimde-dev` | Provides SIMDe headers used by OAI PHY and utility code. |
| `libconfig-dev` | Provides libconfig headers/libraries for OAI configuration support. |
| `libssl-dev` | Provides OpenSSL headers/libraries used by security and crypto-related code paths. |
| `libyaml-cpp-dev` | Provides YAML C++ support used by OAI configuration code. |
| `zlib1g-dev` | Provides zlib development files used by compressed/data handling dependencies. |
| `libreadline-dev` | Provides readline development files used by interactive tools and dependencies. |
| `libncurses-dev` | Provides terminal UI support used by readline and related tooling. |
| `libpcre2-dev` | Provides PCRE2 development files used by regex-capable dependencies. |

Do not rely on Ubuntu's `asn1c` package for OAI. Ubuntu `asn1c` does not support the `-gen-APER` option required by this OAI revision. The local CMake preset below forces OAI to build/use its managed ASN.1 compiler inside the build tree.

Quick package check:

```bash
dpkg-query -W build-essential cmake ninja-build git pkg-config \
  autoconf automake autotools-dev libtool m4 bison flex libfl-dev \
  libsctp-dev libsimde-dev libconfig-dev libssl-dev libyaml-cpp-dev \
  zlib1g-dev libreadline-dev libncurses-dev libpcre2-dev
```

## 2. Workspace

Use this workspace file only to open the OAI source and study notes together:

```json
{
    "folders": [
        {
            "path": "../openairinterface5g",
            "name": "openairinterface5g"
        },
        {
            "path": "../openairinterface5g-study-notes",
            "name": "study-notes"
        }
    ]
}
```

Keep CMake/C++ settings out of `openairinterface5g-study.code-workspace`. The OAI folder's `.vscode/settings.json` is the single local editor settings source.

## 3. CMake User Preset

Create `openairinterface5g/CMakeUserPresets.json`:

```json
{
    "version": 3,
    "configurePresets": [
        {
            "name": "vscode-intellisense",
            "inherits": "default",
            "binaryDir": "${sourceDir}/cmake_targets/ran_build/vscode-intellisense",
            "cacheVariables": {
                "AUTO_DOWNLOAD_ASN1C": "ON",
                "ASN1C_EXEC": "",
                "ASN1C_EXEC_PATH": "",
                "CMAKE_EXPORT_COMPILE_COMMANDS": "ON",
                "CPM_SOURCE_CACHE": "${sourceDir}/cmake_targets/ran_build/cpm-cache"
            }
        }
    ],
    "buildPresets": [
        {
            "name": "vscode-5gdefault",
            "inherits": "5gdefault",
            "configurePreset": "vscode-intellisense"
        }
    ]
}
```

Why each non-obvious setting exists:

| Setting | Why |
| --- | --- |
| `inherits: default` | Reuses OAI's tracked default configure preset, including the Ninja generator and `RelWithDebInfo`. |
| `binaryDir` | Keeps generated build files under OAI's ignored `cmake_targets/ran_build/` area. |
| `AUTO_DOWNLOAD_ASN1C=ON` | Allows OAI to build a compatible ASN.1 compiler if the system one is unsuitable. |
| `ASN1C_EXEC` / `ASN1C_EXEC_PATH` empty | Prevents CMake from selecting Ubuntu `/usr/bin/asn1c`, which lacks OAI's required `-gen-APER` support. |
| `CMAKE_EXPORT_COMPILE_COMMANDS=ON` | Generates `compile_commands.json` for VS Code C/C++ IntelliSense. |
| `CPM_SOURCE_CACHE` | Keeps CPM downloads inside the ignored OAI build area instead of `~/.cache/cpm`. |
| `vscode-5gdefault` inherits `5gdefault` | Reuses OAI's default 5G target list while binding it to the editor-oriented configure preset. |

## 4. VS Code Folder Settings

Create `openairinterface5g/.vscode/settings.json`:

```json
{
    "cmake.useCMakePresets": "always",
    "cmake.configureOnOpen": false,
    "cmake.configurePreset": "vscode-intellisense",
    "cmake.buildPreset": "vscode-5gdefault",
    "C_Cpp.default.configurationProvider": "",
    "C_Cpp.default.compileCommands": "${workspaceFolder}/cmake_targets/ran_build/vscode-intellisense/compile_commands.json",
    "C_Cpp.default.compilerPath": "/usr/bin/gcc",
    "C_Cpp.default.intelliSenseMode": "linux-gcc-x64"
}
```

Why each setting exists:

| Setting | Why |
| --- | --- |
| `cmake.useCMakePresets` | Makes CMake Tools use `CMakePresets.json` and `CMakeUserPresets.json`. |
| `cmake.configureOnOpen=false` | Prevents VS Code from configuring OAI automatically before the local preset is ready. |
| `cmake.configurePreset` | Selects the local editor/build configure preset. |
| `cmake.buildPreset` | Selects the local 5G build preset. |
| `C_Cpp.default.configurationProvider=""` | Keeps IntelliSense on the explicit `compile_commands.json` path instead of CMake Tools' provider mode. |
| `C_Cpp.default.compileCommands` | Points C/C++ IntelliSense to the compilation database generated by CMake. |
| `C_Cpp.default.compilerPath` | Uses the host GCC compiler for system include discovery. |
| `C_Cpp.default.intelliSenseMode` | Matches a Linux GCC x86_64 environment. |

## 5. Configure And Generate Editor Artifacts

Run from `openairinterface5g/`:

```bash
cmake --fresh --preset vscode-intellisense
cmake --build --preset vscode-5gdefault --target generate_T gen_nr_rrc_hdrs -- -j2
```

Useful checks:

```bash
cmake --list-presets=all
cmake --build --preset vscode-5gdefault --target help
stat cmake_targets/ran_build/vscode-intellisense/compile_commands.json
```

Expected build-cache signals after configure:

| Cache value | Expected value |
| --- | --- |
| `CMAKE_GENERATOR` | `Ninja` |
| `PKG_CONFIG_EXECUTABLE` | `/usr/bin/pkg-config` |
| `sctp_INCLUDE_DIR` | `/usr/include` |
| `sctp_LIBRARY` | `/usr/lib/x86_64-linux-gnu/libsctp.so` |

## 6. Local File Policy

These files are local and ignored by the OAI repository:

```text
openairinterface5g/CMakeUserPresets.json
openairinterface5g/.vscode/
openairinterface5g/cmake_targets/ran_build/
```

Do not commit generated build outputs or personal editor settings to OAI unless the team intentionally decides to share them.
