# Task Audit Workflow — Quick Reference

**Goal**: Audit stale tasks, identify overlaps, refactor with research, get Claude approval

---

## WORKFLOW AT A GLANCE

```
1. PLANNING AGENT (Local - You provide stale task file)
   ↓
   Creates: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/
   - ANALYSIS.md (issues + phase routing gate)
   - RESEARCH_ASSIGNMENT.md (concrete research questions)
   ↓
2. QWEN (Local - Separate session)
   ↓
   Determines: What phase does this belong in?
   Research results → /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/qwen-research/
   ↓
3. GEMINI (Web - Single session)
   ↓
   Input: Original task + ANALYSIS.md + qwen-research findings
   Creates: 
   - REFINED_TASK.md (refined version of original)
   - FINAL_SUMMARY.md (for Claude review + phase routing recommendation)
   ↓
   If Gemini needs MORE research mid-synthesis:
   → Adds to RESEARCH_ASSIGNMENT.md
   → You can run quick Qwen follow-up
   ↓
4. CLAUDE (Free web)
   ↓
   Reviews FINAL_SUMMARY.md + phase routing decision
   Approves or requests changes
   ↓
5. POST-APPROVAL (You execute)
   ↓
   Original: /tasks/review/[TASK].md → /tasks/reference/[TASK].md (design archival)
   Refined: /refactored-task-files/.../REFINED_TASK.md → 
     - /tasks/active/phase5/ (if Phase 5 Luna MVP)
     - /tasks/backlog/phase6+/ (if Phase 6+ queued)
     - /docs/new_agent/.../design/ (if Design/Reference)
   Day folder: Archived as audit trail
```

---

## PLANNING AGENT (Step 1) — Your Job

**Input**: Stale task file path (from you)

**Output**: Two files created
1. `ANALYSIS.md` — Issues + questions for Gemini
2. `RESEARCH_ASSIGNMENT.md` — Concrete research questions for Qwen

**Time**: ~1-2 hours

**Templates**:
- Use `TEMPLATE_ANALYSIS.md` as format
- Use `TEMPLATE_RESEARCH_ASSIGNMENT.md` as format

**Stop Point**: Wait for Qwen research completion

---

## QWEN PHASE (Step 2) — Local Research

**Input**: RESEARCH_ASSIGNMENT.md (handoff from planning agent)

**Output**: Research files created
- Location: `/refactored-task-files/YYYY-MM-DD/qwen-research/`
- Primary: `RESEARCH_FINDINGS.md` (phase routing recommendation + research answers)
- Supporting: validation reports, codebase audit, design analysis, etc.
**Research Complete — Create Handoff**:
- Create `QWEN_RESEARCH_COMPLETE_HANDOFF.md` at `/refactored-task-files/YYYY-MM-DD/`
- Lists the 4 files strategist should compile for Gemini
- Summarizes research findings + phase routing for strategist context
**Key Output**: Phase routing decision — "This belongs in Phase [X] because..."

**Time**: Varies by research scope (~1-3 hours typical)

---

## GEMINI (Step 3) — Web (Single Session)

**Input**: 
- Original task file (`/tasks/review/[TASK].md`)
- ANALYSIS.md + RESEARCH_ASSIGNMENT.md
- Qwen's research findings

**Output**: Refined task + Summary
- `REFINED_TASK.md` — Refined/updated version of original task
- `FINAL_SUMMARY.md` — For Claude review (includes phase routing decision + research locations + original task reference)

**Mid-synthesis behavior**: If Gemini needs MORE research, she adds to RESEARCH_ASSIGNMENT.md. You can run a quick Qwen follow-up if needed.

**Time**: ~30-45 min

**Location**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/gemini-draft/`

---

## CLAUDE (Step 4) — Final Review

**Input**: `FINAL_SUMMARY.md` + research references

**Output**: Approval or feedback
- ✅ Approve → Move to next step
- ⚠️ Changes → Back to Gemini
- ❌ Blocker → Resolve first

**Time**: ~15-30 min (minimal)

---

## POST-APPROVAL (Step 5) — File Movement

**Original task** (preserved for design reference):
```bash
mv /tasks/review/TASK_NAME.md /tasks/reference/TASK_NAME.md
```

**Refined task** (routed to appropriate destination):

If Phase 5 Luna MVP:
```bash
mv /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/gemini-draft/REFINED_TASK.md /Users/tam0013/Documents/git/galaxyGame/galaxy_game/lib/tasks/active/phase5/YYYY-MM-DD-TASK-NAME.md
```

If Phase 6+:
```bash
mv /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/gemini-draft/REFINED_TASK.md /Users/tam0013/Documents/git/galaxyGame/galaxy_game/lib/tasks/backlog/phase6+/YYYY-MM-DD-TASK-NAME.md
```

If Design/Reference:
```bash
mv /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/gemini-draft/REFINED_TASK.md /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/design/YYYY-MM-DD-CONCEPT-NAME.md
```

**Day folder**: Stays in place as audit trail

---

## FILE LOCATIONS REFERENCE

| File | Location | Status |
|------|----------|--------|
| Original task | `/tasks/review/` | Before audit |
| Planning analysis | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/ANALYSIS.md` | Audit work |
| Research assignment | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/RESEARCH_ASSIGNMENT.md` | Audit work |
| Qwen research | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/qwen-research/` | Audit work |
| Refined task | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/gemini-draft/REFINED_TASK.md` | Draft |
| Final summary | `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/refactored-task-files/YYYY-MM-DD/gemini-draft/FINAL_SUMMARY.md` | Claude review |
| **After approval:** | | |
| Original (reference) | `/tasks/reference/` | Design archival |
| Refined (Phase 5) | `/tasks/active/phase5/` | Ready to work |
| Refined (Phase 6+) | `/tasks/backlog/phase6+/` | Queued for future phase |
| Refined (Design) | `/docs/new_agent/.../design/` | Reference material |

---

## TEMPLATES PROVIDED

- `PLANNING_AGENT_WORKFLOW.md` — This workflow overview
- `TEMPLATE_ANALYSIS.md` — Copy for ANALYSIS.md
- `TEMPLATE_RESEARCH_ASSIGNMENT.md` — Copy for research questions (renamed from GEMINI_BRIEF)
- `TEMPLATE_FINAL_SUMMARY.md` — Copy for FINAL_SUMMARY.md
