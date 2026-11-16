# QWEN HANDOFF: Research Phase

**From**: Planning Agent  
**Date**: YYYY-MM-DD  
**Original Task**: /tasks/review/[TASK_NAME].md  
**Work Folder**: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/

---

## Your Role

**Research Agent** — Execute research to answer concrete questions about a stale task. This determines what phase the task belongs in (Phase 5 active, Phase 6+ queued, or Design reference).

---

## What You Receive

The planning agent has already created two files in your work folder:
1. **ANALYSIS.md** — Planning agent's analysis (issues, overlaps, concerns)
2. **RESEARCH_ASSIGNMENT.md** — Your research questions (what to investigate)

**Read both before starting.**

---

## Your Job

Answer the research questions in RESEARCH_ASSIGNMENT.md by investigating:
- Luna mission rake integration (Phase 5 relevance)
- Current codebase status vs task requirements
- Game design concept validity
- (Any additional questions Gemini adds during synthesis)

---

## ⚠️ IMPORTANT: Phase Classifications Are Unreliable

**DO NOT TRUST phase labels in the old task.** Agents have classified phases inconsistently in the past.

**Only valid sources**:
- `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/PHASE_STRUCTURE.md` — Canonical phase definitions
- `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/PHASE_DEFINITIONS.md` — Additional detail

**What to do**: Review canonical phase docs first. Then re-classify this task based on **actual phase definitions**, not old labels in the task file.

---

## What to Create

**Primary output**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/qwen-research/RESEARCH_FINDINGS.md`

**Format**:
- Answer each research question with concrete findings
- Include phase routing recommendation (Phase 5 / Phase 6+ / Design/Reference + reasoning)
- Reference specific code files/commits
- Include technical analysis (feasibility, blockers, dependencies)

**Optional supporting files** (if needed):
- Code audit reports
- Design analysis
- Performance assessments
- etc.

---

## Success Criteria

When done, strategist should be able to:
- ✅ See what phase this task belongs in (with clear reasoning)
- ✅ Understand current codebase status vs task requirements
- ✅ Know whether game design concepts are worth preserving
- ✅ Pass findings to Gemini for task synthesis

---

## When Done

1. Create RESEARCH_FINDINGS.md with answers to all questions
2. Create **QWEN_RESEARCH_COMPLETE_HANDOFF.md** (use `TEMPLATE_QWEN_RESEARCH_COMPLETE_HANDOFF.md`):
   - List the 4 files strategist should compile for Gemini
   - Summarize your research findings
   - Include phase routing recommendation + reasoning
   - Include any technical blockers or game design concepts to preserve
3. Update this handoff file: Add "Status: COMPLETE" to top
4. Report back: "Research complete, files ready. See QWEN_RESEARCH_COMPLETE_HANDOFF.md"

---

## Next Steps (After You Complete)

1. Strategist reads QWEN_RESEARCH_COMPLETE_HANDOFF.md
2. Strategist compiles the 4 files listed there
3. Strategist passes to Gemini for synthesis
4. Gemini creates refined task + FINAL_SUMMARY.md
5. Claude reviews and approves

**You are done after research findings and handoff are saved.**

