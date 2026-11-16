# Qwen Handoff — Guardrails Migration Path Validation

**Role**: Local Validation & Verification Agent  
**Task**: Parallel validation of the unified guardrails task  
**Scope**: Verify section-to-folder mapping, file structure prerequisites, and risk mitigation  

---

## CONTEXT: UNIFIED TASK SUMMARY

Gemini just completed reconciliation work, producing: `2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md`

**Key Findings**:
- Stale task (2026-06-21) provides high-level goal
- Gemini's audit provides surgical mapping with economic validation
- Unified task supersedes stale task with HIGH priority
- Next step recommendation: Parallel Qwen review then executor handoff

---

## YOUR ASSIGNMENT

Read the unified reconciliation task (in same folder) and validate three critical areas:

### 1. SECTION-TO-FOLDER MAPPING VERIFICATION

**What to check**:
- Gemini's audit identified specific sections from the 681-line `docs/GUARDRAILS.md` should move to specific target folders
- Verify these target folders actually exist in the current codebase:
  - `docs/architecture/` (subdirectories for stations, logistics, etc.)
  - `docs/mission_profiles/`
  - `docs/systems/` (for em_power_shield_tiers.md)

**Questions to answer**:
1. Are the target directories present? List what exists.
2. Are there any subdirectories that DON'T exist yet and need creation?
3. Do any of the target files already exist with content that might conflict with the migration?

### 2. FILE STRUCTURE PREREQUISITES CHECK

**What to check**:
- The unified task lists specific files that will be created (e.g., `docs/architecture/stations/em_power_shield_tiers.md`)
- Verify the parent directories exist to receive these files

**Questions to answer**:
1. Can you trace the full path for each new file listed? (Example: does `docs/architecture/stations/` exist, or does it need to be created?)
2. Are there any "orphan" target directories (referenced in mapping but don't exist)?
3. What's the pre-work needed before executor can proceed?

### 3. RISK MITIGATION VALIDATION

**Risk Flags from unified task**:
- Drift risk: agent-tasks/rules/GUARDRAILS.md desync from project root
- Dependency: Architecture docs must exist before deleting source content
- Integration: "Market vs. Build" logic must be preserved

**Questions to answer**:
1. Is `agent-tasks/rules/GUARDRAILS.md` currently synchronized with `docs/GUARDRAILS.md`? (Check if identical or drifted)
2. Are the economic constants (SCC Surcharge 0.5%, Broker Fee 0.3%, Sales Tax 3.37%) present in `GLOSSARY_SYSTEM_MECHANICS.md`?
3. Can you identify where "Market vs. Build" logic exists in the current codebase so it's not lost?

---

## REQUIRED DELIVERABLE

**File**: `2026-07-01-TASK-GUARDRAILS-VALIDATION-QWEN.md`

**Sections**:
1. **Mapping Verification Result** (2-3 sentences): Are target folders present and ready to receive migrated content?
2. **Prerequisites Checklist** (bulleted list): What directories/files need to exist before executor starts?
3. **Pre-Work Tasks** (bulleted list): Any file creation or setup needed first?
4. **Risk Mitigation Summary** (bulleted list): Status of drift risk, dependency check, integration check
5. **Blockers Identified** (if any): Things that would prevent executor success
6. **Recommended Action**: Is this ready for executor immediately, or does pre-work need to happen first?

---

## KEY FILES TO REFERENCE

All in `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01-TASK-UPDATE-2/`:
- `2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md` (Gemini's unified task)
- `GUARDRAILS_REF_PACKAGE.md` (Gemini's audit with detailed mapping)
- `TASK_REF_GUARDRAILS_MIGRATION_V2.md` (Original detailed task spec)

Search targets in codebase:
- `docs/GUARDRAILS.md` (source file to split)
- `docs/GLOSSARY_SYSTEM_MECHANICS.md` (canonical economic constants reference)
- `docs/architecture/` (target directory tree)
- `docs/mission_profiles/` (target directory)
- `agent-tasks/rules/GUARDRAILS.md` (sync target — check status)

---

## SUCCESS CRITERIA

✅ All target directories identified and verified  
✅ Prerequisites for executor clearly documented  
✅ Risk mitigation status confirmed  
✅ Any blockers surfaced and flagged  
✅ Executor can proceed with clear understanding of pre-work needed  

---

## WORKFLOW

1. Read unified reconciliation task
2. Inspect codebase to validate mapping and prerequisites
3. Document findings in the deliverable file above
4. Return file and we'll evaluate: immediate executor handoff or pre-work phase first?

---

## SESSION CLEANUP (If all approved and moved to final location)

After validation report is reviewed and final task is moved to `/2026-07-01-TASK-UPDATE-2/`, clean up temp folder:

```bash
rm -rf /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/_working-temp/*
```

This keeps the workspace clean for the next planning session.
