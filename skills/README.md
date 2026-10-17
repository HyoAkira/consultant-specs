# Consultant skills

Each folder is one skill. Each skill has a `SKILL.md` and may have a
`templates/` folder with the output structure it writes.

Start with `consultant-onboard`. Do not call the other skills directly
unless you already know which document or tool you need.

## Conventions

- **In-place updates:** Tool outputs and documents are written to a plain
  filename (e.g. `MECE-S1.md`, not `MECE-S1-v1.md`). The version number is
  tracked in `change.yaml` as metadata. A `-v<n>.md` snapshot is only
  created when content changes significantly or the user requests it.
- **Shared report protocol:** All seven analysis tools reference the Report
  Protocol in `consultant-onboard/SKILL.md` instead of carrying their own
  copy. This keeps the skill files short and avoids drift.
- **Silent mode:** Tools proceed without stopping when the task is clear.
  Reasoning and assumptions are recorded in the report, not in extra
  questions. Only genuine blockers interrupt the user.
- **File naming:** Do not edit these files by hand if you later move to a
  generator.
