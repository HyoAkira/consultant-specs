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
The output file is `RED-TEAM-S<stage>.md` (updated in-place; see onboarding).

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
2. If `stages.<stage>.steps.red_team.status` is `running`, ask the user
   whether to reset it.
3. Read `REPORT.md` (cached from onboarding if available). Follow the
   **Shared Report Protocol** in `consultant-onboard/SKILL.md`.
   Finding prefix of this tool: `RED`.
4. Set `stages.<stage>.steps.red_team.status` to `running`. Leave the version.
5. Use the template `templates/red-team.md` (skip re-reading if in context).
6. In silent mode, derive the plan from the change description and findings.
   Otherwise, ask the user for the plan or proposal. Then gather the
   assumptions the plan depends on. If the user cannot list them, propose
   assumptions from the findings in the report and mark them `Proposed`.
7. For each assumption, write:
   - The attack: the strongest argument that it is false
   - The disproof: the evidence that would show it is false
   - The impact if false: High, Medium, or Low
8. Name the weakest points of the plan.
9. Write a verdict: which assumptions, if false, break the plan.
10. Give each assumption an ID. Run the consistency check (Shared Report
    Protocol).
11. Update `ProjectDocs/changes/<slug>/RED-TEAM-S<stage>.md` in-place. If
    the file does not exist yet, create it. Only create a `-v<n>.md` snapshot
    if the content changed significantly or the user explicitly requests it.
12. Do the steps in "After you write the tool file" in the Shared Report
    Protocol.
13. Show a short summary. Name the next recommended tool. In silent mode,
    continue to the next step.

## Report protocol

This tool follows the **Shared Report Protocol** defined in
`consultant-onboard/SKILL.md`. Finding prefix: `RED`.

If a write fails, set the status of your step back to `not_run` and report
the error.
