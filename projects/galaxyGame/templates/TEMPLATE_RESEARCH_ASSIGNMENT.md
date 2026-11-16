# TEMPLATE — RESEARCH_ASSIGNMENT.md

**For**: Qwen (local research session)  
**From**: Planning Agent  
**Date**: YYYY-MM-DD  
**Original Task**: `/tasks/review/[TASK_FILENAME].md`

---

## ⚠️ IMPORTANT: Phase Classifications Are Unreliable

**DO NOT TRUST phase labels in the old task.** Agents have classified phases inconsistently in the past.

**Only valid sources**:
- `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/PHASE_STRUCTURE.md` — Canonical phase definitions
- `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/PHASE_DEFINITIONS.md` — Additional detail

**What to do**: Review canonical phase docs first. Then re-classify this task based on **actual phase definitions**, not old labels in the task file.

---

## Context

We're auditing a stale task and need to determine what phase it belongs in and whether it still has relevant game design information. Before Gemini synthesizes a replacement, we need concrete research findings.

**Phase Destinations**:
- **Phase 5 Luna MVP**: Luna mission profile validation (5a), settlement option testing (5b), AI training (5c) — Unblocks luna_mission.rake
- **Phase 6+**: Luna worldhouse, depots, shipyards, Mars/Venus/Psyche footholds — Queued for after Phase 5 completes
- **Design/Reference**: Game design documentation, architecture patterns, concepts

---

## Research Questions (Priority Order)

### 1. Phase Relevance & Luna Mission Rake Impact

**What to investigate**:
- Does this task relate to luna_mission.rake execution or Phase 5 Luna settlement testing?
- If YES: Which phase (5a/5b/5c)? What does it unblock?
- If NO: What phase should it belong in (Phase 6+, Design, Reference)?
- Would removing this task break luna_mission.rake or leave a gap?

**Success criteria**: You can definitively answer: "This belongs in Phase [X] because..."

---

### 2. Current Codebase Status vs Task Requirements

**What to investigate**:
- What does the current codebase actually have?
- What does the task require?
- Is there overlap? Is the task already partially done?
- What's still missing?

**Success criteria**: You can answer: "Current state: [what exists]. Task requires: [what's needed]. Gap: [what's missing]"

---

### 3. Game Design Validity

**What to investigate**:
- Does the original task contain valid game design concepts (even if implementation timing is off)?
- Are the requirements still strategically sound, or are they outdated?
- Would this be useful reference material for Phase 6+ work?

**Success criteria**: You can answer: "This design idea is [still valid / outdated / partially valid] because..."

---

## Research Output

Save findings to: `/refactored-task-files/YYYY-MM-DD/qwen-research/`

**Create**:
- `RESEARCH_FINDINGS.md` (answers to questions above + phase routing recommendation)
- Any supporting files (codebase audit, code diffs, design analysis, etc.)

**Format findings so Gemini can**:
- Understand what phase this task belongs in
- See what's missing vs. current codebase
- Decide if game design concepts are worth preserving
- Create appropriate refined task (active work, reference, or queued for future phase)

---

## Mandatory Context

**Read before starting research**:
- Original task: `/tasks/review/[TASK_FILENAME].md`
- Planning analysis: `/refactored-task-files/YYYY-MM-DD/ANALYSIS.md`
- Phase structure: `/docs/new_agent/projects/galaxy_game/tasks/backlog/PHASE_STRUCTURE.md`
- Luna mission rake: `/galaxy_game/lib/tasks/luna_mission.rake`

---

## Additional Research (As Needed During Synthesis)

If Gemini (during Phase 2) needs MORE research, she'll add to this assignment. Check back after Gemini session and run any new questions if needed.

---

## When Done

Return: `/refactored-task-files/YYYY-MM-DD/qwen-research/RESEARCH_FINDINGS.md` with phase routing recommendation

Next: Gemini will synthesize research + analysis → refined task for Claude review
