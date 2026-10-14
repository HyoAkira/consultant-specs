---
name: consultant-mece
description: MECE analysis tool for the Consultant framework. Splits a topic into branches that do not overlap and cover everything. Use from consultant-onboard or when the user asks for MECE, mutually exclusive collectively exhaustive, or structured decomposition. Takes one argument, stage (1 to 7).
license: GPL-3.0
metadata:
  author: Umut Can AKÇAY
  version: "1.2"
---

# MECE Tool

MECE means mutually exclusive, collectively exhaustive. Each branch must
differ from the others. All branches together must cover the whole topic.

## Stage argument

This tool takes one argument, `stage`, a number from 1 to 7. The stage sets
the focus of the analysis. Use the focus of the stage that you were given.
The output file is `MECE-S<stage>-v<n>.md`.

If the stage is not in the table, tell the user that this tool is not used
in that stage. Ask the user to choose a stage from the table.

| Stage | Focus |
|---|---|
| 1 | Split the problem, the symptoms, the goals, and the expected benefits. Find missing or overlapping items. |
| 2 | Classify the solution categories, the user needs, and the unmet requirements. |
| 3 | Split the scope, the out-of-scope topics, the goals, and the success criteria. Find missing or conflicting items. |
| 4 | Split the existing resources and constraints into complete categories. |
| 5 | Split the work, the stages, the dependencies, and the deliverables. |
| 6 | Organize the functional and non-functional requirements, the user scenarios, and the acceptance criteria. |
| 7 | Check the coverage and completeness of the documents. Find needless repetition. |

Findings to register: each branch (status `Info`) and each coverage gap
(status `Open`).

## Procedure

1. Read `ProjectDocs/config.yaml` and `ProjectDocs/changes/<slug>/change.yaml`.
   If the slug is not clear, ask the user which change to use.
   Confirm the `stage` argument. If it is missing, ask the user.
2. If `stages.<stage>.steps.mece.status` is `running`, ask the user whether to reset it.
3. Read `ProjectDocs/project.md` and `REPORT.md`. Follow "Before you ask the
   user any question" in the report protocol.
4. Set `stages.<stage>.steps.mece.status` to `running` in change.yaml. Leave the version.
5. Read `templates/mece.md`.
6. Ask the user for the topic and the split criteria. Ask questions one at a
   time. Mark unknown items as `Unknown`.
7. Build 3 to 7 branches. Each branch needs one clear criterion.
8. Check overlap: compare each pair of branches. Move shared items to one
   branch and note the change.
9. Check coverage: ask "Is there any item that fits no branch?" Add a
   branch or note the gap.
10. Give each branch and each gap an ID. Run the consistency check in the
    report protocol.
11. Write `ProjectDocs/changes/<slug>/MECE-S<stage>-v<n>.md`, where n = the version of this
    step + 1.
    Use the template.
12. Read the file back. Confirm the sections exist.
13. Do the steps in "After you write the tool file" in the report protocol.
14. Show a short summary. Name the next recommended tool. Stop.

## Report protocol

Every tool follows this protocol. The report is
`ProjectDocs/changes/<slug>/REPORT.md`. It holds all findings of the change.
Do not write the same finding twice.

Finding prefix of this tool: `MECE`.

### Before you ask the user any question

1. Read `REPORT.md`.
2. Read the Findings Register. Use its findings as known facts.

### After the analysis and before you write the tool file

1. Give each new finding an ID. Use the prefix `MECE-` and the next free
   number in the register, for example `MECE-01`.
2. Set the Evidence value of each new finding:
   - `Verified`: the user gave evidence, or a source supports it.
   - `Inferred`: you derived it from other findings or known facts.
   - `Untested`: it is an assumption or a hypothesis.
   Do not mark a finding `Verified` on your own judgement.
3. Run the consistency check:
   - Conflict: a new finding contradicts a finding in the register.
     Record both IDs.
   - Open item: a finding from another tool relates to your topic and has the
     status `Open`. Address it in this analysis, or state that you did not.
   - Gap: a topic that this tool normally covers is missing from the register.
4. Tell the user each conflict, open item, and gap. Record the user's
   decision in the note.
5. If the user decides that an earlier finding no longer holds, mark the
   dependent work as stale. Set `stale: true` in `change.yaml` for the step
   that wrote the earlier finding (the register shows its tool and stage) and
   for each document in the `Addressed in` column of that finding. Do not run
   them again yourself.
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
   version that no longer applies to `Superseded`. Do not delete any row. Set `stale: true`
   for each document in the `Addressed in` column of those findings.
6. Update the `Updated` date.
7. Set your step to `done` in `change.yaml`. Write the new version and the
   file name. Set `stale` to `false` for your step.
8. If the report status is `done`, set it to `running`.

If a write fails, set the status of your step back to `not_run` and report
the error.
