---
name: consultant-de-bono
description: De Bono six thinking hats tool for the Consultant framework. Reviews a proposal from six fixed viewpoints, one at a time. Use from consultant-onboard or when the user asks for six hats, De Bono, or structured parallel thinking. Takes one argument, stage (1 to 7).
license: GPL-3.0
metadata:
  author: Umut Can AKÇAY
  version: "1.2"
---

# De Bono Tool

Six thinking hats review one proposal from six modes. Each hat has one
mode. Run the hats in the fixed order below. Do not mix modes inside a hat.

| Hat | Mode | Question |
|---|---|---|
| White | Facts | What facts do we know? What data is missing? |
| Red | Feelings | What is the gut reaction? Which concerns are emotional? |
| Black | Caution | What could go wrong? What are the risks? |
| Yellow | Benefits | What value does this create? Why could it work? |
| Green | Creativity | What other options exist? |
| Blue | Process | What is the summary? What is the next step? |

## Stage argument

This tool takes one argument, `stage`, a number from 1 to 7. The stage sets
the focus of the analysis. Use the focus of the stage that you were given.
The output file is `DE-BONO-S<stage>-v<n>.md`.

If the stage is not in the table, tell the user that this tool is not used
in that stage. Ask the user to choose a stage from the table.

| Stage | Focus |
|---|---|
| 2 | Produce new solution and differentiation hypotheses from the research findings. Use different viewpoints on purpose. |
| 3 | Evaluate the scope decisions from different viewpoints. Find alternatives. |
| 4 | Produce alternative uses of resources, learning paths, and solution approaches. |
| 5 | Evaluate alternative plans, opportunities, and ways to use resources. |
| 6 | Review the requirements from different viewpoints: user needs, benefits, negative effects, and alternatives. |
| 7 | Evaluate the final project definition from the viewpoints of facts, benefits, criticism, creativity, and process. |

Findings to register: each missing fact from the White hat (status `Open`),
each risk from the Black hat (status `Open`), and each alternative from the
Green hat (status `Info`).

## Procedure

1. Read `ProjectDocs/config.yaml` and `ProjectDocs/changes/<slug>/change.yaml`.
   If the slug is not clear, ask the user which change to use.
   Confirm the `stage` argument. If it is missing, ask the user.
2. If `stages.<stage>.steps.de_bono.status` is `running`, ask the user whether to reset it.
3. Read `ProjectDocs/project.md` and `REPORT.md`. Follow "Before you ask the
   user any question" in the report protocol.
4. Set `stages.<stage>.steps.de_bono.status` to `running`. Leave the version.
5. Read `templates/de-bono.md`.
6. Ask the user to state the proposal in two to three sentences.
7. Run the hats in order. For each hat, ask the user for input when
   needed. Keep the answers inside that hat's mode. If the user gives an
   answer for a different hat, record it under the correct hat.
8. Give each missing fact, risk, and alternative an ID. Run the consistency
   check in the report protocol.
9. Write `ProjectDocs/changes/<slug>/DE-BONO-S<stage>-v<n>.md`, where n = the version of this
    step + 1.
10. Read the file back. Confirm all six hat sections exist.
11. Do the steps in "After you write the tool file" in the report protocol.
12. Show a short summary. Name the next recommended tool. Stop.

## Report protocol

Every tool follows this protocol. The report is
`ProjectDocs/changes/<slug>/REPORT.md`. It holds all findings of the change.
Do not write the same finding twice.

Finding prefix of this tool: `DEB`.

### Before you ask the user any question

1. Read `REPORT.md`.
2. Read the Findings Register. Use its findings as known facts.

### After the analysis and before you write the tool file

1. Give each new finding an ID. Use the prefix `DEB-` and the next free
   number in the register, for example `DEB-01`.
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
