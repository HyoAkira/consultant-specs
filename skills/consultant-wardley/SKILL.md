---
name: consultant-wardley
description: Wardley mapping tool for the Consultant framework. Places user needs and components on an evolution axis and proposes strategic moves. Use from consultant-onboard or when the user asks for Wardley mapping, value chain, or build versus buy analysis. Takes one argument, stage (1 to 7).
license: GPL-3.0
metadata:
  author: Umut Can AKÇAY
  version: "1.2"
---

# Wardley Tool

A Wardley map links user needs to the components that serve them. Each
component has a visibility (how close to the user) and an evolution stage:
Genesis, Custom, Product, or Commodity. The map shows which components to
build, buy, or differentiate.

## Stage argument

This tool takes one argument, `stage`, a number from 1 to 7. The stage sets
the focus of the analysis. Use the focus of the stage that you were given.
The output file is `WARDLEY-S<stage>.md` (updated in-place; see onboarding).

If the stage is not in the table, tell the user that this tool is not used
in that stage. Ask the user to choose a stage from the table.

| Stage | Focus |
|---|---|
| 2 | Study the value chain, the critical capabilities, and the strategic position. Use them to evaluate the solution options. |
| 4 | Evaluate the capabilities, the dependencies, and the strategic importance of the resources that the project needs. |
| 5 | Reflect the critical capabilities, the dependencies, and the strategic priorities in the roadmap. |
| 7 | Check again that the strategic choices agree with the resource and dependency decisions. |

Findings to register: each strategic move (status `Open`) and each skill or
resource gap (status `Open`).

## Procedure

1. Read `ProjectDocs/config.yaml` and `ProjectDocs/changes/<slug>/change.yaml`.
   If the slug is not clear, ask the user which change to use.
   Confirm the `stage` argument. If it is missing, ask the user.
2. If `stages.<stage>.steps.wardley.status` is `running`, ask the user
   whether to reset it.
3. Read `REPORT.md` (cached from onboarding if available). Follow the
   **Shared Report Protocol** in `consultant-onboard/SKILL.md`.
   Finding prefix of this tool: `WAR`.
4. Set `stages.<stage>.steps.wardley.status` to `running`. Leave the version.
5. Use the template `templates/wardley.md` (skip re-reading if in context).
6. In silent mode, derive user needs and components from the change
   description and findings. Otherwise, ask the user for the project's user
   needs and the components that serve them.
7. For each component, set visibility from 1 (user-facing) to 5 (hidden), and
   set its evolution stage. Explain each stage choice in one sentence.
8. Record dependencies between components.
9. Write strategic moves: build, buy, partner, or differentiate. Tie each
   move to a component and a reason.
10. Note skill and resource gaps that the map reveals.
11. Give each strategic move and each gap an ID. Run the consistency check
    (Shared Report Protocol).
12. Update `ProjectDocs/changes/<slug>/WARDLEY-S<stage>.md` in-place. If the
    file does not exist yet, create it. Only create a `-v<n>.md` snapshot if
    the content changed significantly or the user explicitly requests it.
13. Do the steps in "After you write the tool file" in the Shared Report
    Protocol.
14. Show a short summary. Name the next recommended tool. In silent mode,
    continue to the next step.

## Report protocol

This tool follows the **Shared Report Protocol** defined in
`consultant-onboard/SKILL.md`. Finding prefix: `WAR`.

If a write fails, set the status of your step back to `not_run` and report
the error.
