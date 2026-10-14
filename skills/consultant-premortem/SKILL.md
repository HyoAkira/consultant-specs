---
name: consultant-premortem
description: Pre-mortem tool for the Consultant framework. Assumes the project has already failed and writes plausible failure stories with warning signs and mitigations. Use from consultant-onboard or when the user asks for a pre-mortem or failure analysis before work starts. Takes one argument, stage (1 to 7).
license: GPL-3.0
metadata:
  author: Umut Can AKÇAY
  version: "1.2"
---

# Pre-Mortem Tool

A pre-mortem starts from a failed outcome. The project has failed at its
target date. The task is to explain why. Each failure story needs a cause,
a warning sign, and a mitigation.

## Stage argument

This tool takes one argument, `stage`, a number from 1 to 7. The stage sets
the focus of the analysis. Use the focus of the stage that you were given.
The output file is `PREMORTEM-S<stage>-v<n>.md`.

If the stage is not in the table, tell the user that this tool is not used
in that stage. Ask the user to choose a stage from the table.

| Stage | Focus |
|---|---|
| 2 | Assume that the proposed solution has failed. Produce the possible failure scenarios. |
| 3 | Assume that the project with the chosen scope has failed. Examine the failure scenarios. |
| 4 | Examine the failures that a shortage of resources, a lack of knowledge, or external dependencies can cause. |
| 5 | Assume that the roadmap has failed. Identify the possible failure scenarios. |
| 6 | Examine the failure scenarios that missing or wrong requirements can cause. |
| 7 | Identify the failure scenarios that remain for the whole project approach. |

Findings to register: each failure story (status `Open`).

## Procedure

1. Read `ProjectDocs/config.yaml` and `ProjectDocs/changes/<slug>/change.yaml`.
   If the slug is not clear, ask the user which change to use.
   Confirm the `stage` argument. If it is missing, ask the user.
2. If `stages.<stage>.steps.premortem.status` is `running`, ask the user whether to reset it.
3. Read `ProjectDocs/project.md` and `REPORT.md`. Follow "Before you ask the
   user any question" in the report protocol.
4. Set `stages.<stage>.steps.premortem.status` to `running`. Leave the version.
5. Read `templates/premortem.md`.
6. Ask the user for the target date and the definition of failure. Ask
   questions one at a time.
7. State the failure: "It is <target date>. The project has failed. The
   reason is not yet known."
8. Write failure stories. Each story must be plausible and specific to this
   project.
9. For each story, record:
   - Cause
   - Early warning sign that can be observed before the failure
   - Mitigation
   - Owner, if the user names one
10. Give each story an ID. Run the consistency check in the report protocol.
11. Write `ProjectDocs/changes/<slug>/PREMORTEM-S<stage>-v<n>.md`, where n = the version of this
    step + 1.
12. Read the file back. Confirm the sections exist.
13. Do the steps in "After you write the tool file" in the report protocol.
14. Show a short summary. Name the next recommended tool. Stop.

## Report protocol

Every tool follows this protocol. The report is
`ProjectDocs/changes/<slug>/REPORT.md`. It holds all findings of the change.
Do not write the same finding twice.

Finding prefix of this tool: `PRE`.

### Before you ask the user any question

1. Read `REPORT.md`.
2. Read the Findings Register. Use its findings as known facts.

### After the analysis and before you write the tool file

1. Give each new finding an ID. Use the prefix `PRE-` and the next free
   number in the register, for example `PRE-01`.
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
