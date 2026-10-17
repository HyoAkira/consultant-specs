# Consultant

A document-first framework for planning a project before implementation.
It produces PRD, ADR and SPEC documents, runs seven analysis tools, and
keeps one cumulative report per change. Completed changes are archived and
merged into a single project file.

## Entry point

Run `/consultant-onboard`. It creates the project root, opens new changes,
and continues existing ones. All other skills are called from onboard.

## Skills

| Skill | Purpose |
|---|---|
| `consultant-onboard` | Entry point. New change, continue change, stage guidance |
| `consultant-mece` | MECE decomposition |
| `consultant-ishikawa` | Ishikawa (fishbone) root cause analysis |
| `consultant-de-bono` | De Bono six thinking hats |
| `consultant-red-team` | Red teaming of assumptions |
| `consultant-wardley` | Wardley mapping |
| `consultant-premortem` | Pre-mortem failure analysis |
| `consultant-fmea` | Failure mode and effects analysis |
| `consultant-document` | PRD, ADR and SPEC documents (`type` argument) |
| `consultant-report` | Finalizes the cumulative report of a change |
| `consultant-archive` | Archive a change and merge its SPEC delta into `project.md` |

## Workflow

A change runs through seven stages. `consultant-onboard` tracks the stage
state, shows the current stage, recommends the next step, and closes each
stage.

**Silent mode (default):** When a task is clear enough to proceed, onboard
runs the recommended step automatically without asking. It only stops at
blocking decision points — conflicts, stale work, or when the user needs to
decide. To switch to interactive mode, tell the agent to ask you before
running a step.

**In-place updates (default):** Tool outputs and documents are updated in
place. A `-v<n>.md` version file is created only when the content changes
significantly or you explicitly request a version snapshot. The version
number is metadata in `change.yaml`, not part of the filename.

1. Project justification and benefit
2. Existing solutions and differentiation (starts with research)
3. Scope and success criteria
4. Knowledge, resources, and constraints
5. Roadmap and feasibility
6. Requirements definition
7. Holistic review and completion of documentation

Each stage has steps in a recommended order. You may run any step of any
stage in any order, or skip a step. Each tool has its own focus for each
stage. State is kept per stage and step in `change.yaml`.

## The cumulative report

Each change has one living report, `REPORT.md`.

- Every tool and document reads it first and updates it after. The report
  protocol is defined once in `consultant-onboard/SKILL.md` and referenced by
  all seven analysis tools — no duplication.
- Every finding gets an ID (`MECE-01`, `ISH-01`, ...). Tools cite IDs. They
  do not repeat findings.
- Every tool runs a consistency check against the report. It records
  conflicts, open items, and gaps in the report.
- Every finding has an Evidence value: `Verified`, `Inferred`, or
  `Untested`. Tools may also use `Assumed` for reasonable assumptions.
- When a conflict shows that an earlier finding no longer holds, the tool
  marks the dependent tool and documents as `stale`. Onboard recommends to
  run them again first. Archive warns about stale work.
- `consultant-report` finalizes the report when each of the seven tools has
  been run.

## Output layout

```
ProjectDocs/
  config.yaml
  project.md
  changes/<slug>/
    change.yaml
    REPORT.md
    MECE-S1.md ... FMEA-S7.md
    PRD.md, ADR.md, SPEC.md
  archive/YYYY-MM-DD-<slug>/
```

Files are updated in place by default. A `-v<n>.md` file appears only when a
version snapshot is explicitly created.

## Install

Install every skill with the [skills CLI](https://skills.sh):

```bash
npx skills add HyoAkira/consultant-specs
```

Install a single skill with `-s`:

```bash
npx skills add HyoAkira/consultant-specs -s consultant-onboard
```

The CLI detects your agent (Claude Code, Codex, OpenCode, Cursor, Copilot,
and others) and places the skills in the correct directory. To install
manually, copy the folders you need from `skills/` into the skills
directory of your agent.

## License

MIT. See `LICENSE`.

The skills are plain Markdown with no runtime and no dependencies, so MIT
keeps them usable inside proprietary codebases. Contributions welcome.
