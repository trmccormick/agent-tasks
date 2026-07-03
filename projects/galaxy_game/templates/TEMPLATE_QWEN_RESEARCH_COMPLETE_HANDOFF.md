# TEMPLATE — QWEN_RESEARCH_COMPLETE_HANDOFF.md

**From**: Qwen (Research Agent)  
**To**: Strategist (Next step: Gemini synthesis)  
**Date**: YYYY-MM-DD  
**Task**: [TASK_NAME]

---

## Research Complete ✅

Research phase is done. Below are the **4 files to compile and pass to Gemini** in the next session.

---

## Files for Gemini

**Pass exactly these 4 files**:

1. **Original Task** (reference):
   ```
   /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/review/[TASK_FILENAME].md
   ```
   Purpose: Gemini sees original scope + requirements

2. **Planning Analysis** (planning findings):
   ```
   /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/ANALYSIS.md
   ```
   Purpose: Issues identified, questions for Gemini, planning agent's assessment

3. **Research Assignment** (research context):
   ```
   /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/RESEARCH_ASSIGNMENT.md
   ```
   Purpose: Shows Qwen what was asked to research (context for findings)

4. **Research Findings** (research results):
   ```
   /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/qwen-research/RESEARCH_FINDINGS.md
   ```
   Purpose: Qwen's concrete findings + phase routing recommendation

---

## Research Summary

**Phase Routing**: [PHASE DESTINATION — e.g., "Phase 14+" or "Design/Reference"]

**Key Findings**:
- Finding 1: [key point]
- Finding 2: [key point]
- Finding 3: [key point]
- Finding 4: [key point]

**Technical Blockers** (if any):
- Blocker 1: [what needs redesign/fixing]
- Blocker 2: [what needs redesign/fixing]

**Game Design Preserved** (if applicable):
- Concept 1: [valid concept to preserve]
- Concept 2: [valid concept to preserve]

---

## Gemini's Next Task

Gemini will:
1. Read all 4 files
2. Synthesize findings into a refined task
3. Create `REFINED_TASK.md` (updated version of original)
4. Create `FINAL_SUMMARY.md` (for Claude review, includes phase routing decision)
5. Save both to `/gemini-draft/`

---

## Qwen's Final Step: Create Gemini Synthesis Handoff

Before reporting research complete, also create:

**GEMINI_SYNTHESIS_HANDOFF.md** at:
```
/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/GEMINI_SYNTHESIS_HANDOFF.md
```

**Format**:
- Title: HANDOFF FOR GEMINI — Synthesis Phase
- Lists the 4 files (reference this QWEN_RESEARCH_COMPLETE_HANDOFF.md)
- Instructions for Gemini: Create REFINED_TASK.md + FINAL_SUMMARY.md
- Tells Gemini what each file should contain
- Use template: `TEMPLATE_GEMINI_SYNTHESIS_HANDOFF.md`

This way the strategist has:
1. QWEN_RESEARCH_COMPLETE_HANDOFF.md → which 4 files to pass
2. GEMINI_SYNTHESIS_HANDOFF.md → what to ask Gemini to do

---

## ⚠️ SAVE THIS FILE TO _working-temp

**CRITICAL**: Save this handoff file itself to `_working-temp/` before reporting research complete:

```
/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/_working-temp/YYYY-MM-DD-QWEN-RESEARCH-COMPLETE-HANDOFF.md
```

**Then report**: "Research complete. QWEN_RESEARCH_COMPLETE_HANDOFF.md and GEMINI_SYNTHESIS_HANDOFF.md saved to _working-temp."

---

## Ready for Strategist

When both handoff files are created, report back:
- "Research complete"
- "QWEN_RESEARCH_COMPLETE_HANDOFF.md ready"
- "GEMINI_SYNTHESIS_HANDOFF.md ready"

Strategist will then pass all files + handoffs to Gemini.

