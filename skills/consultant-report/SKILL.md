---
name: consultant-report
description: Finalizes the cumulative report of a Consultant change. Writes the executive summary, decisions, requirements, and open issues into REPORT.md. Use when the user asks for the final report, or from consultant-onboard when each of the seven tools has been run.
license: GPL-3.0
metadata:
  author: Umut Can AKÇAY
  version: "1.1"
---

# Report Tool

The report is `ProjectDocs/changes/<slug>/REPORT.md`. It is one living file.
Each tool and document adds its findings while it runs. This skill
finalizes the report. It does not repeat findings.

## Procedure

1. Read `ProjectDocs/config.yaml` and `ProjectDocs/changes/<slug>/change.yaml`.
   If the slug is not clear, ask the user which change to use.
2. Check that each of the seven tools has at least one step with
   `status: done`, in any stage. If a tool has none, stop and list those
   tools. Do not change the report.
3. If `report.status` is `done`, ask the user whether to finalize again.
4. Read `REPORT.md`. Read the PRD, ADR, and SPEC files named in
   `change.yaml`, if they exist.
5. List each finding with the status `Open` and each Consistency Note
   without a decision. Tell the user. Ask for a decision for each one. Write
   the decision in the note, or in Open Issues.
6. Write the sections that say "Written when the report is finalized":
   - Executive Summary: a short summary. Cite finding IDs. Do not copy
     finding text.
   - Decisions: each ADR record with its ID, title, and status. Cite the
     finding IDs it addresses.
   - Requirements: each requirement name under ADDED, MODIFIED, and REMOVED.
   - Open Issues: each finding that is still `Open`, each unresolved
     Consistency Note, and each question that stays open in the documents.
     Cite IDs.
7. Set `Status: Final` in the report header. Update the `Updated` date.
8. Read the file back. Confirm the sections exist.
9. Set `report` to `done` in `change.yaml`.
10. Show a short summary. Name the next step: `consultant-archive`. Stop.

If a write fails, set `report` back to `running` and report the error.
