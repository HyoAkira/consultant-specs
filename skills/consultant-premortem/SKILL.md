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
The output file is `PREMORTEM-S<stage>.md` (updated in-place; see onboarding).

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
2. If `stages.<stage>.steps.premortem.status` is `running`, ask the user
   whether to reset it.
3. Read `REPORT.md` (cached from onboarding if available). Follow the
   **Shared Report Protocol** in `consultant-onboard/SKILL.md`.
   Finding prefix of this tool: `PRE`.
4. Set `stages.<stage>.steps.premortem.status` to `running`. Leave the version.
5. Use the template `templates/premortem.md` (skip re-reading if in context).
6. In silent mode, derive the target date and definition of failure from the
   change context. Otherwise, ask the user for the target date and the
   definition of failure.
7. State the failure: "It is <target date>. The project has failed. The
   reason is not yet known."
8. Write failure stories. Each story must be plausible and specific to this
   project.
9. For each story, record:
   - Cause
   - Early warning sign that can be observed before the failure
   - Mitigation
   - Owner, if the user names one
10. Give each story an ID. Run the consistency check (Shared Report
    Protocol).
11. Update `ProjectDocs/changes/<slug>/PREMORTEM-S<stage>.md` in-place. If
    the file does not exist yet, create it. Only create a `-v<n>.md` snapshot
    if the content changed significantly or the user explicitly requests it.
12. Do the steps in "After you write the tool file" in the Shared Report
    Protocol.
13. Show a short summary. Name the next recommended tool. In silent mode,
    continue to the next step.

## Report protocol

This tool follows the **Shared Report Protocol** defined in
`consultant-onboard/SKILL.md`. Finding prefix: `PRE`.

If a write fails, set the status of your step back to `not_run` and report
the error.
