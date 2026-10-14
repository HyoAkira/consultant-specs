---
name: consultant-ishikawa
description: Ishikawa (fishbone) root cause analysis tool for the Consultant framework. Groups possible causes of a problem into categories and tests candidate root causes. Use from consultant-onboard or when the user asks for fishbone, Ishikawa, or root cause analysis. Takes one argument, stage (1 to 7).
license: GPL-3.0
metadata:
  author: Umut Can AKÇAY
  version: "1.2"
---

# Ishikawa Tool

Ishikawa analysis states one problem as an effect. It lists possible causes
in categories. It tests each cause to find root causes.

## Stage argument

This tool takes one argument, `stage`, a number from 1 to 7. The stage sets
the focus of the analysis. Use the focus of the stage that you were given.
The output file is `ISHIKAWA-S<stage>-v<n>.md`.

If the stage is not in the table, tell the user that this tool is not used
in that stage. Ask the user to choose a stage from the table.

| Stage | Focus |
|---|---|
| 1 | Group the possible root causes of the defined problem by category. |
| 2 | Group the possible causes of the failure scenarios that were identified. |
| 3 | Group the possible causes of the failures that relate to the scope. |
| 4 | Group the possible causes of problems that come from resources and skills. |
| 5 | Examine the possible causes of the failures that were identified. |
| 6 | Analyze the causes of problems that come from the requirements. |
| 7 | Check whether the possible causes of the critical risks are examined enough. |

Findings to register: each root cause candidate (status `Open`). Write
`Confirmed` in the Note column if the user gave evidence.

## Procedure

1. Read `ProjectDocs/config.yaml` and `ProjectDocs/changes/<slug>/change.yaml`.
   If the slug is not clear, ask the user which change to use.
   Confirm the `stage` argument. If it is missing, ask the user.
2. If `stages.<stage>.steps.ishikawa.status` is `running`, ask the user whether to reset it.
3. Read `ProjectDocs/project.md` and `REPORT.md`. Follow "Before you ask the
   user any question" in the report protocol.
4. Set `stages.<stage>.steps.ishikawa.status` to `running`. Leave the version.
5. Read `templates/ishikawa.md`.
6. Ask the user to state the problem as one effect, in one sentence. Ask
   for the most important facts about it. Ask questions one at a time.
7. Use these categories. Change them only if the project needs it, and
   note the change in the output:
   - People
   - Process
   - Tools and Technology
   - Data
   - Environment
   - Requirements
8. For each category, ask for possible causes. Add causes the user gives.
   Do not add causes the user did not mention without marking them as
   `Proposed`.
9. For each cause, ask "Why?" three to five times. Record the chain.
10. Mark the last cause in each chain as a root cause candidate. Mark it
    `Confirmed` only if the user gives evidence.
11. Give each root cause candidate an ID. Run the consistency check in the
    report protocol.
12. Write `ProjectDocs/changes/<slug>/ISHIKAWA-S<stage>-v<n>.md`, where n = the version of this
    step + 1.
13. Read the file back. Confirm the sections exist.
14. Do the steps in "After you write the tool file" in the report protocol.
15. Show a short summary. Name the next recommended tool. Stop.

## Report protocol

Every tool follows this protocol. The report is
`ProjectDocs/changes/<slug>/REPORT.md`. It holds all findings of the change.
Do not write the same finding twice.

Finding prefix of this tool: `ISH`.

### Before you ask the user any question

1. Read `REPORT.md`.
2. Read the Findings Register. Use its findings as known facts.

### After the analysis and before you write the tool file

1. Give each new finding an ID. Use the prefix `ISH-` and the next free
   number in the register, for example `ISH-01`.
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
