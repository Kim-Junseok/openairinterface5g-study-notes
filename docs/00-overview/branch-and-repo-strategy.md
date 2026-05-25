# Branch and Repository Strategy

Status: draft  
Source baseline: `openairinterface5g@cb0e501293a7a4664f09322136d7ff29a39343dc`

## Recommendation

Create a separate GitHub repository named `openairinterface5g-study-notes` for study notes.

Do not commit personal study notes directly to the OAI `develop` branch.

## Why Separate Repository Is Better

- The local OAI `origin` points to the official GitLab upstream, not a personal GitHub repo.
- OAI `develop` is an integration branch that changes over time.
- Study notes, diagrams, Korean explanations, and experiment logs are personal knowledge artifacts, not necessarily upstreamable changes.
- A separate repo can have its own README, issue tracker, roadmap, tags, and release snapshots.
- It avoids mixing local notes with OAI source changes when rebasing, pulling, or preparing patches.

## When to Create a Branch in OAI

Create a branch inside the OAI repository only when the change belongs to OAI itself:

- Fixing OAI source code.
- Adding or correcting upstream OAI documentation.
- Preparing a merge request against OAI.
- Running a controlled experiment that modifies code and should be preserved as a patch.

Suggested branch names:

- `docs/study-note-links`
- `experiment/nr-sa-rfsim`
- `fix/<short-problem-name>`

## Suggested Local Workflow

```bash
# 1. Keep OAI as source checkout
cd /path/to/openairinterface5g
git checkout develop
git pull --ff-only origin develop
git rev-parse HEAD

# 2. Keep notes as separate GitHub repo
cd /path/to/openairinterface5g-study-notes
git init
git add .
git commit -m "Bootstrap OAI study notes"
git branch -M main
git remote add origin git@github.com:<user>/openairinterface5g-study-notes.git
git push -u origin main
```

## Optional Cross-Linking

If the notes need to point to the exact source checkout:

- Record OAI commit hashes in `docs/99-references/source-baseline.md`.
- Use relative paths like `openair2/LAYER2/NR_MAC_gNB/` in notes.
- Consider adding the OAI repository as a Git submodule only if exact source snapshots must be bundled with notes.

Default recommendation: do not use a submodule at first. Commit hashes are enough for early study notes.

