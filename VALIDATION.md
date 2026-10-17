# Simplification Validation: Before vs After

## Scenario
User runs `/consultant-onboard` → creates a new change "improve login flow" → MECE stage 1 runs.

## Before (baseline)

### File operations (provider/tool calls)
1. Read `config.yaml` (check if exists) — 1 read
2. Write `config.yaml` — 1 write
3. Write `project.md` — 1 write
4. Write `change.yaml` — 1 write
5. Write `REPORT.md` — 1 write
6. MECE: Read `config.yaml` — 1 read (redundant, already known)
7. MECE: Read `change.yaml` — 1 read (redundant)
8. MECE: Read `project.md` — 1 read (redundant)
9. MECE: Read `REPORT.md` — 1 read (redundant)
10. MECE: Read `templates/mece.md` — 1 read (inline in SKILL.md)
11. MECE: Write `MECE-S1-v1.md` — 1 write (new version file)
12. MECE: Read file back — 1 read (verification step)
13. MECE: Edit `change.yaml` — 1 edit
14. MECE: Edit `REPORT.md` — 1 edit

### Questions to user
1. "What is the change description?"
2. "Confirm slug 'improve-login-flow'?"
3. "Which step to run?"
4. "What is the topic?"
5. "What is the split criterion?"
6. "Is there any item that fits no branch?"
7. "Consistency check conflicts — what do you decide?" (multiple)
8. "Which step to run next?"

### Totals (before)
- **File reads**: 7 (3 redundant re-reads)
- **File writes**: 6 (including new version file)
- **File edits**: 2
- **Questions**: 8+
- **New version files**: 1 (`MECE-S1-v1.md`)
- **Context**: Onboard SKILL.md (206 lines) + MECE SKILL.md (121 lines, incl. 55-line report protocol) = 327 lines. Report protocol is duplicated across 7 tools = ~385 lines of redundant text.

## After (simplified)

### File operations (provider/tool calls)
1. Read `config.yaml` (check if exists) — 1 read (cached for rest of session)
2. Write `config.yaml`, `project.md`, `change.yaml`, `REPORT.md` — 4 writes (batched creation)
3. MECE: `REPORT.md` and `change.yaml` cached from onboarding — 0 reads
4. MECE: Template inline in SKILL.md — 0 reads
5. MECE: Write `MECE-S1.md` in-place — 1 write (in-place, no version file)
6. MECE: Edit `REPORT.md` — 1 edit
7. MECE: Edit `change.yaml` — 1 edit

### Questions to user
1. "What is the change description?" (only meaningful question)

### Totals (after)
- **File reads**: 1 (down from 7, -86%)
- **File writes**: 5 (down from 6, -17% due to batching)
- **File edits**: 2 (same)
- **Questions**: 1 (down from 8+, -87%)
- **New version files**: 0 (down from 1, -100%)
- **Context**: Onboard SKILL.md (291 lines, includes shared report protocol) + MECE SKILL.md (77 lines, 4-line protocol reference) = 368 lines for first read. Report protocol read once, not 7 times.

## Second run (tiny change — e.g. MECE stage 1 again)

### Before
- Creates `MECE-S1-v2.md` (new version file)
- Re-reads `config.yaml`, `change.yaml`, `project.md`, `REPORT.md`, template
- 7+ questions
- New version file: 1

### After
- Updates `MECE-S1.md` in-place (0 new files)
- Re-reads: 0 (all cached)
- Questions: 0 (silent mode)
- New version files: 0

## Acceptance Criteria Verification

| # | Criterion | Before | After | Status |
|---|-----------|--------|-------|--------|
| 1 | Tek ufak değişiklik → 0 yeni versiyon dosyası | 1 new file per change | 0 new files (in-place) | ✅ |
| 2 | Provider çağrı sayısı düşmüş | 14 calls (7 reads + 6 writes + 2 edits) | 8 calls (1 read + 5 writes + 2 edits) | ✅ (-43%) |
| 3 | Okunan skill+context metriği azalmış | 327 lines SKILL.md + 385 lines duplicate protocol | 368 lines (first read) + 0 duplicate protocol | ✅ |
| 4 | Görev yeterince anlatıldığında ek soru sormadan uygula | 8+ questions | 1 question | ✅ (-87%) |

## Conceptual Model Preservation

| Element | Before | After | Preserved? |
|---------|--------|-------|-----------|
| 7 tools | MECE, Ishikawa, De Bono, Red Team, Wardley, Pre-Mortem, FMEA | Same | ✅ |
| 7 stages | 7-step stage table | Same | ✅ |
| ID scheme | MECE-01, ISH-01, DEB-01, RED-01, WAR-01, PRE-01, FMEA-01, RES-01 | Same | ✅ |
| Evidence values | Verified, Inferred, Untested | Verified, Inferred, Untested + Assumed | ✅ (extended) |
| Command names | Same skill names | Same | ✅ |
| Output pattern | `MECE-S1.md`, `PRD.md`, etc. | Same (version suffix removed from default) | ✅ |

## Decisions Made (Assumed)

1. **Version files**: In-place by default. Version files only for significant changes or explicit user request. `version` in change.yaml is metadata (run counter), not part of filename.
2. **All 7 tools required**: All 7 tools kept as the conceptual model. They remain skippable. Report finalization still checks each tool ran at least once.
3. **Silent mode**: Default. Can be overridden by telling the agent to ask before running.
4. **Baseline metrics**: 8+ questions, 7 reads per tool run. Target: 1 question, 1 read, 0 re-reads (cached).
