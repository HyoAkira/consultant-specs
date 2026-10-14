---
name: consultant-red-team
description: Red team tool for the Consultant framework. Attacks the assumptions of a plan and tests whether they hold. Use from consultant-onboard or when the user asks for red teaming, assumption testing, or a devil's advocate review. Takes one argument, stage (1 to 7).
license: GPL-3.0
metadata:
  author: Umut Can AKÇAY
  version: "1.2"
---

# Red Team Tool

Red teaming finds the assumptions a plan depends on. It argues that each
one is false. It names the evidence that would prove it false. It ranks
the damage if the assumption is wrong.

## Stage argument

This tool takes one argument, `stage`, a number from 1 to 7. The stage sets
the focus of the analysis. Use the focus of the stage that you were given.
The output file is `RED-TEAM-S<stage>-v<n>.md`.

If the stage is not in the table, tell the user that this tool is not used
in that stage. Ask the user to choose a stage from the table.

| Stage | Focus |
|---|---|
| 1 | Test whether the project is needed, how strong the benefit claims are, and whether other approaches make more sense. |
| 2 | Test the differentiation hypotheses. Find why they can fail and when they become invalid. |
| 3 | Test the scope decisions, the goals, and the success criteria. Find unrealistic expectations. |
| 4 | Test whether the resources are enough. Test the assumptions about skills. |
| 5 | Test the time, the cost, the technical feasibility, and the assumptions of the plan. |
| 6 | Test the reasons, the feasibility, the verifiability, and the possible conflicts of the requirements. |
| 7 | Test the main assumptions, the decisions, the requirements, and the overall plan one last time. |

Findings to register: each assumption (status `Open`). Write the impact if
the assumption is false in the Note column.

## Procedure

1. Read `ProjectDocs/config.yaml` and `ProjectDocs/changes/<slug>/change.yaml`.
   If the slug is not clear, ask the user which change to use.
   Confirm the `stage` argument. If it is missing, ask the user.
2. If `stages.<stage>.steps.red_team.status` is `running`, ask the user whether to reset it.
3. Read `ProjectDocs/project.md` and `REPORT.md`. Follow "Before you ask the
   user any question" in the report protocol.
4. Set `stages.<stage>.steps.red_team.status` to `running`. Leave the version.
5. Read `templates/red-team.md`.
6. Ask the user for the plan or proposal. Then ask for the assumptions the
   plan depends on. If the user cannot list them, propose assumptions from
   the findings in the report and mark them `Proposed`. Ask questions one at
   a time.
7. For each assumption, write:
   - The attack: the strongest argument that it is false
   - The disproof: the evidence that would show it is false
   - The impact if false: High, Medium, or Low
8. Name the weakest points of the plan.
9. Write a verdict: which assumptions, if false, break the plan.
10. Give each assumption an ID. Run the consistency check in the report
    protocol.
11. Write `ProjectDocs/changes/<slug>/RED-TEAM-S<stage>-v<n>.md`, where n = the version of this
    step + 1.
12. Read the file back. Confirm the sections exist.
13. Do the steps in "After you write the tool file" in the report protocol.
14. Show a short summary. Name the next recommended tool. Stop.

## Report protocol

Every tool follows this protocol. The report is
`ProjectDocs/changes/<slug>/REPORT.md`. It holds all findings of the change.
Do not write the same finding twice.

Finding prefix of this tool: `RED`.

### Before you ask the user any question

1. Read `REPORT.md`.
2. Read the Findings Register. Use its findings as known facts.

### After the analysis and before you write the tool file

1. Give each new finding an ID. Use the prefix `RED-` and the next free
   number in the register, for example `RED-01`.
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
