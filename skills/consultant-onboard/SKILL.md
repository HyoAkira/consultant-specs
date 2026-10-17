---
name: consultant-onboard
description: Entry point for the Consultant framework. Use to start a project, open a new change, or continue an existing change. It runs the seven workflow stages. Also use when the user says "consultant onboard" or runs /consultant-onboard.
license: GPL-3.0
metadata:
  author: Umut Can AKÇAY
  version: "1.2"
---

# Consultant Onboard

This is the only entry command. It creates the project root, opens new
changes, and continues existing changes. It runs the seven workflow stages
of a change. It tracks the stage state, recommends the next step, and closes
each stage. It routes the user to the right tool, document, report, or
archive skill.

## Rules

- Write all documents in English.
- Do not edit project code. This framework writes documents only.
- Ask before you overwrite any file that already exists with different content.
- **Silent mode (default):** When a task is clear enough to proceed, do it
  without stopping. Mark reasonable assumptions as `Assumed` or `Inferred`
  in the finding's Evidence column. Only interrupt the user at blocking
  decision points (conflicts, stale work that needs re-running, or when
  findings contradict each other).
- **In-place updates (default):** Small edits update the existing document
  in place. A new `-v<n>.md` file is created only when the content changes
  significantly or the user explicitly requests a version snapshot. The
  version number is metadata in `change.yaml`, not part of the filename.
- Every tool and document reads `REPORT.md` first and updates it after.
  See the **Shared Report Protocol** below. When onboarding calls a tool,
  it keeps `change.yaml` and `REPORT.md` in context so the tool can read
  them once instead of re-reading every time.
- The stage order is a recommendation. The user may run any step of any
  stage, in any order. Accept the choice. Do not block it.
- Do not stop after every file. Continue to the next recommended step in
  silent mode. Only present a summary and wait when the user needs to
  decide, or when a stage is ready to close.

## Step 1: Check the project root

1. Check whether `ProjectDocs/config.yaml` exists.
2. If it does not exist, create the structure automatically:
   1. Run `basename "$(pwd)"`. Use the result as `<project_name>`.
   2. Run `date +%Y-%m-%d`. Use the result as `<date>`.
   3. Create the folders `ProjectDocs/changes/` and `ProjectDocs/archive/`.
   4. Create `ProjectDocs/config.yaml` from `templates/config.yaml`.
      Replace `<project_name>`.
   5. Create `ProjectDocs/project.md` from `templates/project.md`.
      Replace `<project_name>` and `<date>`.
   Do not ask the user for the project name.
3. If it exists, read it. Use its `changes_dir`, `archive_dir`,
   and `project_file` values. Do not assume the defaults.

## Step 2: Choose a change

1. List the folders in `ProjectDocs/changes/`.
2. Show the options:
   - `New change`
   - Each existing change, with its current stage (see Step 4) and the
     number of `done` steps and documents
3. Ask the user to choose one.

## Step 3a: New change

1. Ask for a short description of the change.
2. Suggest a kebab-case slug from the description. Use the slug directly;
   do not ask for confirmation unless the user edits the suggestion.
3. Check that `ProjectDocs/changes/<slug>/` does not exist. If it exists,
   stop and ask the user to choose another slug or continue that change.
4. Get today's date with `date +%Y-%m-%d`.
5. Create `ProjectDocs/changes/<slug>/change.yaml` from
   `templates/change.yaml`. Replace `<slug>`, `<description>`, and
   `<date>`.
6. Create `ProjectDocs/changes/<slug>/REPORT.md` from `templates/report.md`.
   Replace `<change title>` with the description, `<slug>`, and `<date>`.
7. Go to Step 4.

## Step 3b: Continue a change

1. Read `ProjectDocs/changes/<slug>/change.yaml` and `REPORT.md`. Keep both
   in context for the duration of this session.
2. If any step or document has `status: running`, the last run did not
   finish. Ask the user:
   - `Reset and run again` sets the status to `not_run`.
   - `Keep as is` leaves it unchanged. Do not start a new run on it.
   Ignore the report and the stage status here. The report stays `running`
   until it is finalized.
3. If a step with `stale: true` belongs to a stage with `status: done`, set
   that stage to `running`.
4. Go to Step 4.

## Step 4: Run the stages

Use this loop for the whole change.

1. Find the current stage. It is the lowest stage whose status is not
   `done`.
2. Show the stage card:
   - The stage number and name
   - Its steps in the recommended order, each with its status
   - The expected result of the stage (see the stage table)
   - Each step with `stale: true` and the reason (see Consistency Notes)
   - The number of findings with the status `Open` and the number of open
     Consistency Notes
3. Recommend the next step. Choose in this order:
   1. A step with `stale: true`
   2. The first step with `status: not_run`, in the recommended order
4. In silent mode (default), run the recommended step automatically.
   Otherwise, ask the user which step to run. The user may choose any step.
   - To skip a step, set its status to `skipped`. Write `Skipped: <reason>`
     on its line in Stage Summaries. The user may run a skipped step later.
   - The user does not need to run every step in every stage. Important
     decisions and high-impact risks need enough depth. Findings of one step
     must feed the later steps.
5. Run the chosen step:
   - If the stage status is `not_run`, set it to `running`.
   - Tool: follow the `SKILL.md` of the tool. Give it the slug and the stage.
     The slug and REPORT.md are already in context.
   - Research: follow "Research step" below.
6. After the step, in silent mode continue to the next recommended step.
   Otherwise, show the updated stage card, name the next recommended step,
   and stop.
7. When every step of the stage is `done` or `skipped`, close the stage:
   1. For Stage 2 only: show the findings of the stage grouped by Evidence
      value. Ask the user to confirm or change the values. Update the
      Evidence column. List each `Untested` finding as a validation need.
   2. Compare the expected result of the stage with the findings. For each
      part of the expected result that no finding supplies, add a `Gap` to
      Consistency Notes.
   3. Tell the user each finding of the stage with the status `Open`.
   4. Ask the user to confirm that the stage can close.
   5. Write the stage result in Stage Summaries. Cite finding IDs. Do not
      copy finding text.
   6. Set the stage to `done` in `change.yaml`. Go to the next stage.
8. When Stage 7 is `done`, show what remains: the documents that are not
   `done`, the report, and the archive. Documents are not tied to a stage.
   The user may create them at any time.

## Research step (Stage 2)

Stage 2 starts with research on existing solutions. Do the research in this
skill. Use the web search and web fetch tools of the agent. There is no
separate file. The findings go into `REPORT.md`.

1. Set `stages.2.steps.research.status` to `running`. Leave the version.
2. REPORT.md is already in context from onboarding. Use its findings as
   known facts.
3. Search for similar products, alternatives, user needs, user complaints,
   and the known gaps of existing solutions. In silent mode, assume the user
   knows the domain and skip asking about known references.
4. Give each finding an ID with the prefix `RES-` and the next free number.
5. Set the Evidence value of each finding. Use `Verified` only when a source
   supports it. Write the source URL in the Note column. Use `Inferred` or
   `Untested` for the rest.
6. Run the consistency check against the Findings Register: conflicts, open
   items, and gaps. Tell the user each one. Record the user's decision.
   If the user decides that an earlier finding no longer holds, set
   `stale: true` for the step that wrote it and for each document in the
   `Addressed in` column of that finding.
7. Add the findings to the Findings Register. Write `Research` in the Tool
   column and `2` in the Stage column.
8. Replace the Research line in the Stage 2 summary with a short summary.
   Cite IDs. Add each conflict, open item, and gap to Consistency Notes.
9. Update the `Updated` date.
10. Set `stages.2.steps.research` to `done`. Add 1 to the version. Set
    `stale` to `false`.
11. If the report status is `done`, set it to `running`.

If a write fails, set the research status back to `not_run` and report the
error.

## Stage table

The steps are in the recommended order. Each tool has its own focus for each
stage. The focus is in the `SKILL.md` of the tool.

| Stage | Name | Steps | Expected result |
|---|---|---|---|
| 1 | Project justification and benefit | MECE, Red Team, Ishikawa | Problem definition, benefit hypotheses, possible root causes, evidence, and open questions |
| 2 | Existing solutions and differentiation | Research, MECE, Wardley, De Bono, Red Team, Pre-Mortem, Ishikawa, FMEA | Analysis of existing solutions, solution alternatives, differentiation hypotheses, reasons, and validation needs |
| 3 | Scope and success criteria | MECE, Red Team, De Bono, Pre-Mortem, Ishikawa, FMEA | Clear scope, out-of-scope topics, measurable success criteria, MVP limits, and scope risks |
| 4 | Knowledge, resources, and constraints | Wardley, MECE, Red Team, De Bono, Pre-Mortem, Ishikawa, FMEA | Resource inventory, current skills, skill gaps, constraints, external dependencies, and risks |
| 5 | Roadmap and feasibility | MECE, Wardley, Red Team, Pre-Mortem, Ishikawa, FMEA, De Bono | Stages and dependencies, time and cost estimates, feasibility assessment, priorities, and a risk reduction plan |
| 6 | Requirements definition | MECE, De Bono, Red Team, Pre-Mortem, Ishikawa, FMEA | Prioritized requirements, user scenarios, acceptance criteria, traceability links, and open questions |
| 7 | Holistic review and completion of documentation | MECE, Wardley, Red Team, Pre-Mortem, Ishikawa, FMEA, De Bono | Consistency between documents, reasons for decisions, visible risks and uncertainties, traceable requirements, and documentation ready for the development process |

Stage 2 order: do the research first. Then use De Bono to produce
alternatives and differentiation hypotheses. Then use Red Team to test the
hypotheses. Then use the risk analyses. At the end, separate the verified
information, the supported inferences, and the untested hypotheses. The
Evidence review in Step 4 does this.

## Step names

| Step | Skill |
|---|---|
| MECE | `consultant-mece` |
| Ishikawa | `consultant-ishikawa` |
| De Bono | `consultant-de-bono` |
| Red Team | `consultant-red-team` |
| Wardley | `consultant-wardley` |
| Pre-Mortem | `consultant-premortem` |
| FMEA | `consultant-fmea` |
| Research | This skill, "Research step" |
| Document | `consultant-document` with type `prd`, `adr`, or `spec` |
| Report | `consultant-report` |
| Archive | `consultant-archive` |

## Consistency check

The holistic review is not only Stage 7. Every tool does it as part of its
own run in every stage. Each tool reads `REPORT.md` first (or uses the
version already in context from onboarding). It checks its new findings
against the Findings Register. It records conflicts, open items, and gaps
in Consistency Notes.

## Shared Report Protocol

All seven analysis tools follow this protocol. It is defined once here so
that each tool's SKILL.md can reference it without duplicating the text.

The report is `ProjectDocs/changes/<slug>/REPORT.md`. It holds all findings
of the change. Do not write the same finding twice.

Finding prefix of this tool: set per the tool (MECE, ISH, DEB, RED, WAR,
PRE, FMEA).

### Before you ask the user any question

1. Read `REPORT.md` (or use the version already in context from onboarding).
2. Read the Findings Register. Use its findings as known facts.

### After the analysis and before you write the tool file

1. Give each new finding an ID. Use the tool's prefix and the next free
   number in the register, for example `MECE-01`.
2. Set the Evidence value of each new finding:
   - `Verified`: the user gave evidence, or a source supports it.
   - `Inferred`: you derived it from other findings or known facts.
   - `Assumed`: you made a reasonable assumption that the user can override.
   - `Untested`: it is an assumption or a hypothesis.
   Do not mark a finding `Verified` on your own judgement.
3. Run the consistency check:
   - Conflict: a new finding contradicts a finding in the register.
     Record both IDs.
   - Open item: a finding from another tool relates to your topic and has
     the status `Open`. Address it in this analysis, or state that you did
     not.
   - Gap: a topic that this tool normally covers is missing from the register.
4. In silent mode, record reasonable assumptions and proceed. Only tell the
   user about conflicts, open items, and gaps that require a decision.
   Record the user's decision in the note.
5. If the user decides that an earlier finding no longer holds, mark the
   dependent work as stale. Set `stale: true` in `change.yaml` for the step
   that wrote the earlier finding and for each document in the `Addressed in`
   column of that finding. Do not run them again yourself.
6. Cite earlier findings by ID. Do not copy their text.

### After you write the tool file

1. Add each new finding to the Findings Register in `REPORT.md`. Write its
   Tool, Stage, and Evidence value.
2. If the user confirmed a finding of another tool, or gave evidence for it,
   during this run, update the Evidence value of that finding. Write the
   source in the Note column.
3. Replace your line in the summary of your stage in Stage Summaries with a
   short summary. Cite IDs. Do not copy finding text.
4. Add each conflict, open item, and gap to Consistency Notes. Write your
   tool and stage. For a conflict that marked work as stale, write
   `Marked stale: <items>` in the Note.
5. If this is a re-run for the same stage, set each finding of the earlier
   run that no longer applies to `Superseded`. Do not delete any row. Set
   `stale: true` for each document in the `Addressed in` column of those
   findings.
6. Update the `Updated` date.
7. Set your step to `done` in `change.yaml`. Update the `file` field to the
   filename (in-place by default). Do not increment the version unless the
   content changed significantly. Set `stale` to `false` for your step.
8. If the report status is `done`, set it to `running`.

If a write fails, set the status of your step back to `not_run` and report
the error.

## Finish

Show the status of the change: the current stage, the steps, the documents,
and the report. Name the next recommended item. In silent mode, continue to
the next recommended step instead of stopping.
