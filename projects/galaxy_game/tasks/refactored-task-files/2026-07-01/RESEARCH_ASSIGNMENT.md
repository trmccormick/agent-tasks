# RESEARCH ASSIGNMENT: Settlement View Phase 3

**For**: Qwen (local research session)  
**From**: Planning Agent  
**Date**: 2026-07-01  
**Original Task**: `/tasks/review/settlement-view-phase3.md`

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

**Initial assessment**: This is **NOT** related to luna_mission.rake. The rake file uses `TaskExecutionEngineV2` with JSON mission profiles and settlement data models — no JavaScript admin views or canvas rendering are involved. This task should be classified as **Phase 6+** or **Design/Reference**.

---

### 2. Current Codebase Status vs Task Requirements

**What to investigate**:
- What does the current codebase actually have for admin/canvas/monitor views?
- Does any existing monitor canvas code provide a foundation for settlement view?
- What does the task require (65K canvas, sprite atlas, worldhouse grid, construction preview)?
- Is there overlap? Is the task already partially done?
- What's still missing?

**Success criteria**: You can answer: "Current state: [what exists]. Task requires: [what's needed]. Gap: [what's missing]"

**Specific files to audit**:
- `galaxy_game/app/javascript/admin/` — any existing canvas/view controllers
- `galaxy_game/app/views/admin/celestial_bodies/` — existing view templates
- `public/tilesets/` — existing tileset/sprite assets
- Any monitor or canvas-related code in the codebase

---

### 3. Game Design Validity

**What to investigate**:
- Does the original task contain valid game design concepts (even if implementation timing is off)?
- Are the requirements still strategically sound, or are they outdated?
- Would this be useful reference material for Phase 6+ work?
- Is the "high-resolution settlement view" concept still aligned with the game's vision?

**Success criteria**: You can answer: "This design idea is [still valid / outdated / partially valid] because..."

---

### 4. Technical Feasibility (NEW — Critical)

**What to investigate**:
- Is a 65536x32768 canvas feasible in modern browsers?
- What are the memory/performance implications of a 2+ billion pixel canvas?
- Are there tiled/streaming approaches that would be more practical?
- Has WebGL been considered for this scale?

**Success criteria**: You can answer: "The canvas approach is [feasible with caveats / not feasible as specified / needs major redesign] because..."

---

## Research Output

Save findings to: `/refactored-task-files/2026-07-01/qwen-research/`

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
- Original task: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/review/settlement-view-phase3.md`
- Planning analysis: `/Users/tam0013/Documents/git/galaxyGame/refactored-task-files/2026-07-01/ANALYSIS.md`
- Phase structure: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/PHASE_STRUCTURE.md`
- Luna mission rake: `/Users/tam0013/Documents/git/galaxyGame/galaxy_game/lib/tasks/luna_mission.rake`

---

## Additional Research (As Needed During Synthesis)

If Gemini (during Phase 2) needs MORE research, she'll add to this assignment. Check back after Gemini session and run any new questions if needed.

---

## When Done

Return: `/refactored-task-files/2026-07-01/qwen-research/RESEARCH_FINDINGS.md` with phase routing recommendation

Next: Gemini will synthesize research + analysis → refined task for Claude review
