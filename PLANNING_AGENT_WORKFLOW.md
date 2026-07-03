# PLANNING AGENT WORKFLOW — Task Audit & Refinement

**Role**: Local Planning Agent (Qwen)  
**Task**: Audit stale task, identify overlaps, prepare for research & refinement  
**Time**: ~1-2 hours for planning phase

---

## YOUR WORKFLOW

### 1. SETUP (10 minutes)

You receive from strategist:
- **Stale task file location**: e.g., `/tasks/review/TASK_GUARDRAILS_SPLIT.md`
- **Reason for audit**: overlaps found / stale / needs review

Create your work folder:
```bash
mkdir -p /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/{qwen-research,gemini-draft}
```

### 2. ANALYSIS (30-45 minutes)

Read the stale task. Create `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/ANALYSIS.md`:

**Contents**:
- Phase routing gate: Does this belong to Phase 5 Luna MVP, Phase 6+, or Design/Reference?
- Reference to original task: `/tasks/review/[TASK_FILE].md`
- Issues identified (overlaps, stale content, ambiguities)
- Key questions for Gemini
- Concerns about scope, prerequisites, dependencies

**Template provided**: Use `TEMPLATE_ANALYSIS.md`

### 3. RESEARCH ASSIGNMENT (15-20 minutes)

Create `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/RESEARCH_ASSIGNMENT.md`:

**Contents**:
- Concrete research questions (3-5 specific items)
- What Qwen should investigate
- Success criteria for each question
- What answers Gemini will need

**Template provided**: Use `TEMPLATE_RESEARCH_ASSIGNMENT.md`

### 4. QWEN HANDOFF (5 minutes)

Create `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/QWEN_HANDOFF.md`:

**Contents**:
- Role reminder for Qwen (Research Agent)
- What files Qwen receives (ANALYSIS.md + RESEARCH_ASSIGNMENT.md)
- What to create (RESEARCH_FINDINGS.md)
- Success criteria for research output
- What happens next (passes to Gemini)

**Template provided**: Use `TEMPLATE_QWEN_HANDOFF.md`

### 5. GEMINI FILES CHECKLIST (5 minutes)

Create `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/GEMINI_FILES_CHECKLIST.md`:

**Contents**:
- Explicit list of 4 files to pass to Gemini (original task + ANALYSIS.md + RESEARCH_ASSIGNMENT.md + RESEARCH_FINDINGS.md)
- What each file is for
- What Gemini's task is
- Approval chain after Gemini

**Template provided**: Use `TEMPLATE_GEMINI_FILES_CHECKLIST.md`

### 6. SUBMIT & WAIT

You create all 4 files. **Stop here.**

**Next steps** (Qwen runs research):
1. You give QWEN_HANDOFF.md + RESEARCH_ASSIGNMENT.md to Qwen
2. Qwen runs codebase audit + validation
3. Qwen creates `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/qwen-research/RESEARCH_FINDINGS.md`
4. You get research findings back

**After Qwen completes research**:
1. Strategist uses GEMINI_FILES_CHECKLIST.md to compile 4 files
2. Passes them to Gemini (web chat)
3. Gemini synthesizes research into refined task
4. Gemini creates FINAL_SUMMARY.md
5. Claude reviews and approves
6. Day folder archived as audit trail

---

## YOUR FILES (What You Create)

**Must create**:
- ✅ `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/ANALYSIS.md`
- ✅ `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/RESEARCH_ASSIGNMENT.md`
- ✅ `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/QWEN_HANDOFF.md`
- ✅ `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/GEMINI_FILES_CHECKLIST.md`

**Will receive after Qwen research**:
- Research findings: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/2026-07-01/qwen-research/RESEARCH_FINDINGS.md`

**Will not see** (after Qwen research completes):
- Gemini synthesizes research into refined task
- Claude reviews and approves
- You're done! Day folder is archived as audit trail

---

## KEY NOTES

1. **Don't copy the task** — Reference it by location in ANALYSIS
2. **Be specific in research questions** — Generic questions waste research time
3. **Identify overlaps** — Note areas that might conflict with recent work
4. **Flag ambiguities** — Mark unclear requirements for Gemini to address
5. **Keep it concise** — Analysis should be 1-2 pages max
6. **Provide clear handoffs** — QWEN_HANDOFF and GEMINI_FILES_CHECKLIST ensure smooth transitions

---

## TEMPLATES PROVIDED

- `TEMPLATE_ANALYSIS.md` — Copy this format for ANALYSIS
- `TEMPLATE_RESEARCH_ASSIGNMENT.md` — Copy this format for research questions
- `TEMPLATE_QWEN_HANDOFF.md` — Copy for Qwen handoff (incoming research instruction)
- `TEMPLATE_GEMINI_FILES_CHECKLIST.md` — Copy for Gemini file list
- This file: `PLANNING_AGENT_WORKFLOW.md` — Workflow overview

**Note**: After Qwen research completes, Qwen creates `QWEN_RESEARCH_COMPLETE_HANDOFF.md` (using template) to list the 4 files for strategist to pass to Gemini.

---

## SUCCESS CRITERIA

✅ ANALYSIS.md created (references original task, lists issues)  
✅ GEMINI_BRIEF.md created (ready to paste into Gemini chat)  
✅ Both files in `/refactored-task-files/YYYY-MM-DD/`  
✅ Subdirectories created (`qwen-research/`, `gemini-draft/`)  

**Then**: Strategist takes over for Gemini chat phase
