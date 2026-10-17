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
The output file is `FMEA-S<stage>.md` (updated in-place; see onboarding).

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
2. If `stages.<stage>.steps.fmea.status` is `running`, ask the user
   whether to reset it.
3. Read `REPORT.md` (cached from onboarding if available). Follow the
   **Shared Report Protocol** in `consultant-onboard/SKILL.md`.
   Finding prefix of this tool: `FMEA`.
4. Set `stages.<stage>.steps.fmea.status` to `running`. Leave the version.
5. Use the template `templates/fmea.md` (skip re-reading if in context).
6. List the failure modes from the Ishikawa, Red Team, and Pre-Mortem
   findings in the report. In silent mode, score estimates automatically and
   mark them `Estimated`. Otherwise, ask the user for any additional failure
   modes and confirm the estimates.
7. For each failure mode, record the effect, the cause, the current control,
   and the IDs of the source findings.
8. Score S, O, and D from 1 to 10. Mark each score as `Confirmed` if the user
   gave it, or `Estimated` if you set it. Ask the user to confirm estimates.
9. Calculate RPN = S x O x D. Sort the table by RPN, highest first.
10. Give each failure mode an ID. Run the consistency check (Shared Report
    Protocol). Also check: each Ishikawa, Red Team, and Pre-Mortem finding of
    this stage with the status `Open` must be the source of at least one
    failure mode. If not, record it as an open item.
11. Update `ProjectDocs/changes/<slug>/FMEA-S<stage>.md` in-place. If the
    file does not exist yet, create it. Only create a `-v<n>.md` snapshot if
    the content changed significantly or the user explicitly requests it.
12. Do the steps in "After you write the tool file" in the Shared Report
    Protocol.
13. Show a short summary with the top three RPN rows. Name the next
    recommended tool. In silent mode, continue to the next step.

## Report protocol

This tool follows the **Shared Report Protocol** defined in
`consultant-onboard/SKILL.md`. Finding prefix: `FMEA`.

If a write fails, set the status of your step back to `not_run` and report
the error.
