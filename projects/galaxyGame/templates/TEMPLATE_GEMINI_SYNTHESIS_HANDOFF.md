# TEMPLATE — GEMINI_SYNTHESIS_HANDOFF.md

**Task**: [TASK_NAME] audit  
**Date**: YYYY-MM-DD  
**Input Files**: 4 files (from Qwen research phase)

---

## Your Job

Synthesize these 4 research files into a refined task + summary for Claude approval.

---

## Files to Read (In Order)

1. **Original Task** (understand the scope):
   ```
   /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/review/[TASK_FILENAME].md
   ```

2. **Planning Analysis** (issues + context):
   ```
   /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/ANALYSIS.md
   ```

3. **Research Assignment** (what was researched):
   ```
   /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/RESEARCH_ASSIGNMENT.md
   ```

4. **Research Findings** (concrete results):
   ```
   /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/qwen-research/RESEARCH_FINDINGS.md
   ```

---

## What to Create

**Create 2 files in `/gemini-draft/`**:

### 1. REFINED_TASK.md
Location: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/gemini-draft/REFINED_TASK.md`

**Format** (copy structure from original task, update with findings):
- Title: [TASK_NAME] (Refined)
- Phase: [CONFIRMED_PHASE] (from research)
- Scope: Updated based on codebase reality + technical constraints
- Requirements: Refined with research findings
- Blockers: Any technical or architectural blockers from research
- Game Design Notes: Any valid concepts to preserve for future phases
- Next Steps: Recommendation for phase-specific planning

### 2. FINAL_SUMMARY.md
Location: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/gemini-draft/FINAL_SUMMARY.md`

**Contents** (for Claude review):
- Executive Summary (1-2 sentences: what is this task, why was it audited)
- Phase Routing Decision: [PHASE] (reasoning from research)
- Research Used: Links to ANALYSIS + RESEARCH_FINDINGS
- How Refined Differs From Original:
  - What changed (based on research)
  - What blockers were found
  - What game design concepts are worth preserving
- Blockers: Any show-stoppers or redesign requirements
- Claude's Decision Needed: Approve phase routing + refined task, or request changes

---

## Reference

**Use this template for FINAL_SUMMARY.md**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/templates/TEMPLATE_FINAL_SUMMARY.md`

---

## Success Criteria

- ✅ REFINED_TASK.md created (updated version, correct phase, blockers noted)
- ✅ FINAL_SUMMARY.md created (Claude-ready review document)
- ✅ Both saved to `/gemini-draft/`
- ✅ Phase routing clear + reasoning explicit
- ✅ Game design concepts preserved + documented

---

## ⚠️ SAVE THIS FILE TO _working-temp

**CRITICAL**: Save this handoff file itself to `_working-temp/` after creating it:

```
/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/_working-temp/YYYY-MM-DD-GEMINI-SYNTHESIS-HANDOFF.md
```

Do NOT save to the main refactored-task-files folder — _working-temp only.

---

## When Done

Report back with:
- ✅ Both REFINED_TASK.md and FINAL_SUMMARY.md created in `/gemini-draft/`
- ✅ GEMINI_SYNTHESIS_HANDOFF.md saved to `_working-temp/`
- ✅ Phase routing confirmed
- ✅ Ready for Claude review

Next: Claude approves → Files move to appropriate phase backlog
