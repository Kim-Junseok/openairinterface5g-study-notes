# Runtime Infrastructure

Status: draft  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`  
Related paths: `common/config/`, `common/utils/`, `common/utils/LOG/`, `common/utils/T/`, `common/utils/ocp_itti/`

## Areas to Read

| Area | Paths | Questions |
| --- | --- | --- |
| Configuration | `common/config/` | How are config files parsed and exposed to modules? |
| Logging | `common/utils/LOG/` | How are log levels/categories configured? |
| Tracing | `common/utils/T/` | What can be traced, recorded, and replayed? |
| ITTI / task messaging | `common/utils/ocp_itti/` | How do tasks exchange messages? |
| Threading | `common/utils/threadPool/` | Where are worker pools used? |
| Telnet/web control | `common/utils/telnetsrv/`, `common/utils/websrv/` | What runtime introspection/control is available? |

## Reading Notes

- Start with upstream docs under each `DOC/` directory.
- Then identify the public headers and initialization functions.
- Finally trace one runtime use from an executable into the utility layer.

