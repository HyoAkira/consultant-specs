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
The output file is `DE-BONO-S<stage>.md` (updated in-place; see onboarding).

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
2. If `stages.<stage>.steps.de_bono.status` is `running`, ask the user
   whether to reset it.
3. Read `REPORT.md` (cached from onboarding if available). Follow the
   **Shared Report Protocol** in `consultant-onboard/SKILL.md`.
   Finding prefix of this tool: `DEB`.
4. Set `stages.<stage>.steps.de_bono.status` to `running`. Leave the version.
5. Use the template `templates/de-bono.md` (skip re-reading if in context).
6. In silent mode, derive the proposal from the change description and
   findings. Otherwise, ask the user to state the proposal in two to three
   sentences.
7. Run the hats in order. For each hat, use the findings in the report as a
   starting point and add what the user provides. Keep answers inside that
   hat's mode. If the user gives an answer for a different hat, record it
   under the correct hat.
8. Give each missing fact, risk, and alternative an ID. Run the consistency
   check (Shared Report Protocol).
9. Update `ProjectDocs/changes/<slug>/DE-BONO-S<stage>.md` in-place. If the
   file does not exist yet, create it. Only create a `-v<n>.md` snapshot if
   the content changed significantly or the user explicitly requests it.
10. Do the steps in "After you write the tool file" in the Shared Report
    Protocol.
11. Show a short summary. Name the next recommended tool. In silent mode,
    continue to the next step.

## Report protocol

This tool follows the **Shared Report Protocol** defined in
`consultant-onboard/SKILL.md`. Finding prefix: `DEB`.

If a write fails, set the status of your step back to `not_run` and report
the error.
