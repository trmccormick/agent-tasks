# GEMINI FILES CHECKLIST

**Planning Agent Generated**: 2026-07-01  
**Task Under Audit**: settlement-view-phase3  
**Status**: Research complete ✅ — Ready for Gemini synthesis

---

## Files for Gemini (Ready to Compile)

**Pass these 4 files to Gemini**:

1. ✅ **Original task** (reference):
   - `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/review/settlement-view-phase3.md`
   - Purpose: Gemini sees original scope + requirements

2. ✅ **ANALYSIS.md** (planning findings):
   - `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/ANALYSIS.md`
   - Purpose: Phase routing gate, issues identified, questions for Gemini

3. ✅ **RESEARCH_ASSIGNMENT.md** (research brief):
   - `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/RESEARCH_ASSIGNMENT.md`
   - Purpose: Context for what Qwen was asked to research

4. ✅ **RESEARCH_FINDINGS.md** (Qwen's results - COMPLETE):
   - `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/qwen-research/RESEARCH_FINDINGS.md`
   - Purpose: Concrete research answers + phase routing recommendation (Phase 14+)
   - Status: Complete

---

## Gemini's Task

Using these 4 files, Gemini will:
1. **Synthesize** findings into a refined task
2. **Create REFINED_TASK.md** (updated version of original)
3. **Create FINAL_SUMMARY.md** (for Claude review, includes phase routing decision)
4. **Save both** to `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/gemini-draft/`

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

---

## Initial Assessment

Based on ANALYSIS.md + RESEARCH_FINDINGS.md:
- **Confirmed Phase**: Phase 14+ (Player-facing interface, not NPC-only simulation work)
- **Reasoning**: Phases 5-13 are NPC-only AI Manager expansion. Settlement view is a player management UI, belongs to Phase 14+ when players enter the game
- **Key Issues**: Phantom prerequisites, extreme canvas size (8.5GB+ texture data), underspecified sprite atlas, technical redesign required
- **Game Design Preserved**: Sprite atlas specs and worldhouse grid concepts valid for Phase 14+ reference material
- **Blockers**: Canvas size spec impossible as written; requires WebGL tiling redesign or complete rendering engine rewrite

Gemini will synthesize these findings into refined task routed to Phase 14+ backlog.
