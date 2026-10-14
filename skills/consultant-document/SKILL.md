---
name: consultant-document
description: Creates the PRD, ADR, or SPEC document for a Consultant change. Takes one argument, type, which is prd, adr, or spec. Use from consultant-onboard or when the user asks for a PRD, an ADR, or a spec for a change.
license: GPL-3.0
metadata:
  author: Umut Can AKÇAY
  version: "1.1"
---

# Document Tool

This skill writes one of three documents for a change. The argument `type`
selects the document: `prd`, `adr`, or `spec`.

| Type | File | Template | Needs |
|---|---|---|---|
| prd | `PRD-v<n>.md` | `templates/prd.md` | MECE, Red Team (recommended) |
| adr | `ADR-v<n>.md` | `templates/adr.md` | PRD. One file can hold several decision records. |
| spec | `SPEC-v<n>.md` | `templates/spec.md` | PRD, FMEA (recommended) |

## Procedure

1. Read `ProjectDocs/config.yaml` and `ProjectDocs/changes/<slug>/change.yaml`.
   If the slug is not clear, ask the user which change to use.
2. Confirm the `type` argument is `prd`, `adr`, or `spec`. If not, ask.
3. Check the prerequisites in the table. If a prerequisite is missing,
   tell the user which one, and ask whether to continue.
4. If `documents.<type>.status` is `running`, ask the user whether to reset it.
5. Read `ProjectDocs/project.md` and `REPORT.md`. Read the PRD if the type is
   `adr` or `spec`. Follow "Before you ask the user any question" in the
   report protocol below.
6. Set `documents.<type>.status` to `running`. Leave the version.
7. Read `templates/<type>.md`.
8. Ask the user for the facts the document needs. Ask questions one at a
   time. Use the findings in the report as a starting point.
9. Draft the document using the template. Cite finding IDs.
10. Show the draft in full. Ask the user to approve it or request changes.
    Revise until the user approves.
11. Run the open-findings check in "While you draft" in the report protocol.
12. Write `ProjectDocs/changes/<slug>/<FILE>-v<n>.md`, where n = version + 1.
13. Read the file back. Confirm the sections exist.
14. For `spec`: the file is a delta. It uses ADDED, MODIFIED, and REMOVED
    sections. Check that each requirement name is unique within the file.
    Do not write finding IDs in the SPEC file. Write them in the report only.
15. Do the steps in "After you write the document file" in the report
    protocol.
16. Show a short summary. Name the next recommended step. Stop.

## Report protocol (documents)

The report is `ProjectDocs/changes/<slug>/REPORT.md`. It holds all findings
of the change. Do not write the same finding twice.

### Before you ask the user any question

1. Read `REPORT.md`. Use the Findings Register as known facts.
2. Read a tool output file only when a finding does not give enough detail.

### While you draft

1. Cite the finding ID where a requirement, risk, or decision comes from,
   for example `(RED-01)`. Do not copy the finding text.
2. List each finding with the status `Open` that relates to this document
   and that the document does not address. Tell the user. Record the user's
   decision.

### After you write the document file

1. In the Findings Register, set each finding that the document addresses
   to `Addressed`. Write the document item in the `Addressed in` column,
   for example `PRD-v1 FR1`.
2. Add each finding that the document does not address to Consistency Notes
   as an `Open item`.
3. Update the `Updated` date.
4. Set the document to `done` in `change.yaml`. Write the new version and
   the file name. Set `stale` to `false` for the document.
5. If the report status is `done`, set it to `running`.

If a write fails, set the status of the document back to `not_run` and
report the error.
