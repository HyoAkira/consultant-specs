---
name: consultant-fmea
description: FMEA (failure mode and effects analysis) tool for the Consultant framework. Turns problems from Ishikawa, Red Team, and Pre-Mortem into scored failure modes with a risk priority number. Use from consultant-onboard or when the user asks for FMEA or risk scoring. Takes one argument, stage (1 to 7).
license: GPL-3.0
metadata:
  author: Umut Can AKÇAY
  version: "1.2"
---

# FMEA Tool

FMEA turns problems into numbers. Each failure mode gets three scores from 1
to 10. A higher score is worse:

- Severity (S): the size of the effect.
- Occurrence (O): how often the cause happens.
- Detection (D): how hard the failure is to detect in time.

Risk priority number: RPN = S x O x D.

The main input is the findings of Ishikawa, Red Team, and Pre-Mortem in the
report. The user may add more.

## Stage argument

This tool takes one argument, `stage`, a number from 1 to 7. The stage sets
the focus of the analysis. Use the focus of the stage that you were given.
The output file is `FMEA-S<stage>-v<n>.md`.

If the stage is not in the table, tell the user that this tool is not used
in that stage. Ask the user to choose a stage from the table.

| Stage | Focus |
|---|---|
| 2 | Evaluate the important failure modes and risks. Decide the priority areas for review and mitigation. |
| 3 | Prioritize the risks that relate to the scope. Decide the mitigations that are needed. |
| 4 | Prioritize the critical risks from resources and skills. |
| 5 | Prioritize the risks. Decide in which order to handle the mitigations. |
| 6 | Prioritize the failure modes and the effects of critical requirements that are not met. |
| 7 | Review the priority of the remaining risks and their mitigations. |

Findings to register: each failure mode (status `Open`). Write the scores in
the Note column, for example `S7 O7 D6 RPN 294 Estimated`. Always write the
score status (`Confirmed` or `Estimated`) with the RPN.

## Procedure

1. Read `ProjectDocs/config.yaml` and `ProjectDocs/changes/<slug>/change.yaml`.
   If the slug is not clear, ask the user which change to use.
   Confirm the `stage` argument. If it is missing, ask the user.
2. If `stages.<stage>.steps.fmea.status` is `running`, ask the user whether to reset it.
3. Read `ProjectDocs/project.md` and `REPORT.md`. Follow "Before you ask the
   user any question" in the report protocol.
4. Set `stages.<stage>.steps.fmea.status` to `running`. Leave the version.
5. Read `templates/fmea.md`.
6. List the failure modes from the Ishikawa, Red Team, and Pre-Mortem
   findings in the report. Ask the user for any more. Ask questions one at a
   time.
7. For each failure mode, record the effect, the cause, the current control,
   and the IDs of the source findings.
8. Score S, O, and D from 1 to 10. Mark each score as `Confirmed` if the user
   gave it, or `Estimated` if you set it. Ask the user to confirm estimates.
9. Calculate RPN = S x O x D. Sort the table by RPN, highest first.
10. Give each failure mode an ID. Run the consistency check in the report
    protocol. Also check this: each Ishikawa, Red Team, and Pre-Mortem
    finding of this stage with the status `Open` must be the source of at least one failure
    mode. If it is not, record it as an open item.
11. Write `ProjectDocs/changes/<slug>/FMEA-S<stage>-v<n>.md`, where n = the version of this
    step + 1.
12. Read the file back. Confirm the table exists.
13. Do the steps in "After you write the tool file" in the report protocol.
14. Show a short summary with the top three RPN rows. Name the next
    recommended tool. Stop.

## Report protocol

Every tool follows this protocol. The report is
`ProjectDocs/changes/<slug>/REPORT.md`. It holds all findings of the change.
Do not write the same finding twice.

Finding prefix of this tool: `FMEA`.

### Before you ask the user any question

1. Read `REPORT.md`.
2. Read the Findings Register. Use its findings as known facts.

### After the analysis and before you write the tool file

1. Give each new finding an ID. Use the prefix `FMEA-` and the next free
   number in the register, for example `FMEA-01`.
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
