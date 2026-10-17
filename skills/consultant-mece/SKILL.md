---
name: consultant-mece
description: MECE analysis tool for the Consultant framework. Splits a topic into branches that do not overlap and cover everything. Use from consultant-onboard or when the user asks for MECE, mutually exclusive collectively exclusive, or structured decomposition. Takes one argument, stage (1 to 7).
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
The output file is `MECE-S<stage>.md` (updated in-place; see onboarding).

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
2. If `stages.<stage>.steps.mece.status` is `running`, ask the user whether
   to reset it.
3. Read `REPORT.md` (cached from onboarding if available). Follow the
   **Shared Report Protocol** in `consultant-onboard/SKILL.md`.
   Finding prefix of this tool: `MECE`.
4. Set `stages.<stage>.steps.mece.status` to `running` in change.yaml. Leave
   the version.
5. Use the template `templates/mece.md` (skip re-reading if already in
   context).
6. In silent mode, use the change description as the topic. For the split
   criterion, make a reasonable assumption and mark it `Assumed`. Otherwise,
   ask the user for the topic and split criteria. Do not ask one at a time
   unless interactive clarification is needed.
7. Build 3 to 7 branches. Each branch needs one clear criterion.
8. Check overlap: compare each pair of branches. Move shared items to one
   branch and note the change.
9. Check coverage: "Is there any item that fits no branch?" Add a branch or
   note the gap.
10. Give each branch and each gap an ID. Run the consistency check (Shared
    Report Protocol).
11. Update `ProjectDocs/changes/<slug>/MECE-S<stage>.md` in-place. If the
    file does not exist yet, create it. Only create a `-v<n>.md` snapshot if
    the content changed significantly or the user explicitly requests it.
    Use the template.
12. Do the steps in "After you write the tool file" in the Shared Report
    Protocol.
13. Show a short summary. Name the next recommended tool. In silent mode,
    continue to the next step.

## Report protocol

This tool follows the **Shared Report Protocol** defined in
`consultant-onboard/SKILL.md`. Finding prefix: `MECE`.

If a write fails, set the status of your step back to `not_run` and report
the error.
