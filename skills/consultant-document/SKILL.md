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
| prd | `PRD.md` | `templates/prd.md` | MECE, Red Team (recommended) |
| adr | `ADR.md` | `templates/adr.md` | PRD. One file can hold several decision records. |
| spec | `SPEC.md` | `templates/spec.md` | PRD, FMEA (recommended) |

Documents are updated in-place by default. A `-v<n>.md` file is only created
when the content changes significantly or the user explicitly requests it.

## Procedure

1. Read `ProjectDocs/config.yaml` and `ProjectDocs/changes/<slug>/change.yaml`.
   If the slug is not clear, ask the user which change to use.
2. Confirm the `type` argument is `prd`, `adr`, or `spec`. If not, ask.
3. Check the prerequisites in the table. If a prerequisite is missing,
   tell the user which one, and ask whether to continue.
4. If `documents.<type>.status` is `running`, ask the user whether to reset it.
5. Read `REPORT.md` (cached from onboarding if available). Use the Findings
   Register as known facts. Read a tool output file only when a finding does
   not give enough detail.
6. Set `documents.<type>.status` to `running`. Leave the version.
7. Use the template `templates/<type>.md` (skip re-reading if in context).
8. Draft the document using the template. Cite finding IDs. In silent mode,
   make reasonable assumptions and proceed. Otherwise, ask the user for the
   facts the document needs.
9. If interactive, show the draft in full. Ask the user to approve it or
   request changes. Revise until the user approves.
10. Run the open-findings check: list each finding with the status `Open`
    that relates to this document and that the document does not address.
    In silent mode, note them as open items and proceed. Otherwise, tell the
    user and record the decision.
11. Write `ProjectDocs/changes/<slug>/<FILE>.md` (e.g. `PRD.md`). Create it
    if it does not exist. Only create a `-v<n>.md` snapshot if the content
    changed significantly or the user explicitly requests it.
12. For `spec`: the file is a delta. Check that each requirement name is
    unique within the file. Do not write finding IDs in the SPEC file. Write
    them in the report only.
13. Follow the "After you write the document file" section below.
14. Show a short summary. Name the next recommended step. In silent mode,
    continue.

## After you write the document file

1. In the Findings Register, set each finding that the document addresses to
   `Addressed`. Write the document item in the `Addressed in` column,
   for example `PRD FR1`.
2. Add each finding that the document does not address to Consistency Notes
   as an `Open item`.
3. Update the `Updated` date.
4. Set the document to `done` in `change.yaml`. Update the `file` field. Do
   not increment the version unless the content changed significantly.
   Set `stale` to `false` for the document.
5. If the report status is `done`, set it to `running`.

If a write fails, set the status of the document back to `not_run` and
report the error.
