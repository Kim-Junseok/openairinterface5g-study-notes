# Code Reading Plan

Status: draft  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`

## Method

1. Start from a runnable scenario.
2. Identify the executable entry point.
3. Trace configuration loading.
4. Trace task/thread startup.
5. Follow one control-plane procedure.
6. Follow one user-plane data path.
7. Convert each path into a call-flow note.

## First Targets

| Target | Why |
| --- | --- |
| gNB startup | Reveals config, logging, task startup, protocol init |
| nrUE startup | Reveals UE-side structure and PHY/MAC/RRC interaction |
| RRC setup | Good control-plane anchor |
| MAC scheduling | Central to RAN behavior |
| RF simulator | Best first reproducible lab |

## Output Format

Use `templates/source-file-note.md` for file-level notes and `templates/call-flow.md` for procedure-level notes.

