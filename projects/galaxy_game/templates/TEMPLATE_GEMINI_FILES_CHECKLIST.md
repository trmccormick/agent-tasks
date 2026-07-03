# GEMINI FILES CHECKLIST

**Planning Agent Generated**: YYYY-MM-DD  
**Task Under Audit**: [TASK_NAME]  
**Status**: Planning phase complete - Ready for research

---

## Files for Gemini (After Qwen Research Completes)

**Pass these 4 files to Gemini**:

1. ✅ **Original task** (reference):
   - `/tasks/review/[TASK_NAME].md`
   - Purpose: Gemini sees original scope + requirements

2. ✅ **ANALYSIS.md** (planning findings):
   - `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/ANALYSIS.md`
   - Purpose: Phase routing gate, issues identified, questions for Gemini

3. ✅ **RESEARCH_ASSIGNMENT.md** (research brief):
   - `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/RESEARCH_ASSIGNMENT.md`
   - Purpose: Context for what Qwen was asked to research

4. ✅ **RESEARCH_FINDINGS.md** (Qwen's results):
   - `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/qwen-research/RESEARCH_FINDINGS.md`
   - Purpose: Concrete research answers + phase routing recommendation

---

## Gemini's Task

Using these 4 files, Gemini will:
1. **Synthesize** findings into a refined task
2. **Create REFINED_TASK.md** (updated version of original)
3. **Create FINAL_SUMMARY.md** (for Claude review, includes phase routing decision)
4. **Save both** to `/gemini-draft/`

---

## Approval Chain After Gemini

1. Claude reviews FINAL_SUMMARY.md
2. Claude approves phase routing + refined task
3. Files moved to destinations (Phase 5 / Phase 6+ / Design reference)
4. Day folder archived as audit trail

---

## Notes

- **Do NOT pass**: Incomplete findings, internal notes, or draft analysis
- **Only pass**: Final outputs from each phase
- **Critical**: RESEARCH_FINDINGS.md must include phase routing recommendation
- **If Gemini needs more research**: She'll add questions to RESEARCH_ASSIGNMENT.md, you run follow-up Qwen session

