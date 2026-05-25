# openairinterface5g-study-notes

Personal study notes for reading OpenAirInterface 5G (OAI 5G) source code and documenting build notes, runtime behavior, architecture, call flows, and experiments.

## Source Baseline

- Source project: `openairinterface5g`
- Current local branch at note bootstrap: `develop`
- Current local commit at note bootstrap: `cb0e501293a7a4664f09322136d7ff29a39343dc`
- Source remote observed locally: `https://gitlab.eurecom.fr/oai/openairinterface5g.git`

Because the OAI `develop` branch changes over time, each note should record the source commit and related file paths whenever possible.

## Recommended Git Strategy

These notes should not be committed directly to the OAI upstream `develop` branch.

Recommended approach:

1. Create `openairinterface5g-study-notes` as a separate GitHub repository.
2. Keep the OAI source checkout as the upstream reference for reading and experiments.
3. Record the OAI commit, branch, file paths, and experiment environment in the notes.
4. Create a separate topic branch in the OAI repository only when changing OAI source code itself.

Creating a new branch inside the OAI repository is appropriate when preparing actual changes to OAI source code or upstream OAI documentation. For study notes, explanations, experiment logs, and diagrams, a separate GitHub repository is safer and easier to maintain.

## Folder Structure

```text
openairinterface5g-study-notes/
├── AGENTS.md
├── README.md
├── docs/
│   ├── 00-overview/
│   ├── 01-build-and-run/
│   ├── 02-architecture/
│   ├── 03-ran-stack/
│   ├── 04-deployment/
│   ├── 05-code-reading/
│   ├── 06-experiments/
│   └── 99-references/
├── diagrams/
│   ├── architecture/
│   ├── call-flows/
│   └── sequence/
├── assets/
│   └── images/
├── notes/
│   └── daily/
├── templates/
└── scripts/
```

## Companion Repositories

This repository is the output workspace. It uses the local OAI checkout and interview-prep notes as references:

| Repository | Local path | Usage |
| --- | --- | --- |
| `openairinterface5g` | `/home/jjuns/ran-code/openairinterface5g` | Source-code evidence |
| `interview-prep` | `/home/jjuns/interview-prep` | Study prompts and interview framing |
| `openairinterface5g-study-notes` | `/home/jjuns/ran-code/openairinterface5g-study-notes` | Markdown and PlantUML outputs |

See [AGENTS.md](AGENTS.md) for the working rules used when converting code-reading prompts into notes and diagrams.

## Writing Rules

- Add `Source baseline`, `Related paths`, and `Status` near the top of each document.
- Break down code explanations by function, file, or directory whenever possible.
- Mark assumptions as `Hypothesis` and separate them from `Verified` findings.
- For execution logs, record key commands, environment details, results, and failure causes.
- When summarizing upstream OAI documentation, include the original link or local path.
- Use PlantUML files under `diagrams/` for message sequence charts, component diagrams, activity diagrams, and state flows.

## First Reading Order

1. [Repository Map](docs/00-overview/repository-map.md)
2. [Study Roadmap](docs/00-overview/study-roadmap.md)
3. [Branch and Repository Strategy](docs/00-overview/branch-and-repo-strategy.md)
4. [Build Notes](docs/01-build-and-run/build-notes.md)
5. [RAN Stack Index](docs/03-ran-stack/README.md)
6. [Source Baseline](docs/99-references/source-baseline.md)
