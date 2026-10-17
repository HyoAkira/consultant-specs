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
The output file is `ISHIKAWA-S<stage>.md` (updated in-place; see onboarding).

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
2. If `stages.<stage>.steps.ishikawa.status` is `running`, ask the user
   whether to reset it.
3. Read `REPORT.md` (cached from onboarding if available). Follow the
   **Shared Report Protocol** in `consultant-onboard/SKILL.md`.
   Finding prefix of this tool: `ISH`.
4. Set `stages.<stage>.steps.ishikawa.status` to `running`. Leave the version.
5. Use the template `templates/ishikawa.md` (skip re-reading if in context).
6. In silent mode, derive the problem statement from the change description
   and findings. Otherwise, ask the user to state the problem as one effect,
   in one sentence.
7. Use these categories. Change them only if the project needs it, and
   note the change:
   - People, Process, Tools and Technology, Data, Environment, Requirements
8. For each category, gather possible causes. Add causes the user gives.
   Do not add causes the user did not mention without marking them `Proposed`.
9. For each cause, ask "Why?" three to five times. Record the chain.
10. Mark the last cause in each chain as a root cause candidate. Mark it
    `Confirmed` only if the user gives evidence.
11. Give each root cause candidate an ID. Run the consistency check (Shared
    Report Protocol).
12. Update `ProjectDocs/changes/<slug>/ISHIKAWA-S<stage>.md` in-place. If
    the file does not exist yet, create it. Only create a `-v<n>.md` snapshot
    if the content changed significantly or the user explicitly requests it.
13. Do the steps in "After you write the tool file" in the Shared Report
    Protocol.
14. Show a short summary. Name the next recommended tool. In silent mode,
    continue to the next step.

## Report protocol

This tool follows the **Shared Report Protocol** defined in
`consultant-onboard/SKILL.md`. Finding prefix: `ISH`.

If a write fails, set the status of your step back to `not_run` and report
the error.
