---
title: "Research — Does elevation feed into biome/temperature classification?"
priority: MEDIUM
status: backlog
phase: phase1
owner: Research Agent (Qwen)
type: research
system_domain: TERRA_SIM | BIOME_RENDERING
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-07-17
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Research Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/drafts/2026-07-17/2026-07-17-MEDIUM-RESEARCH-ELEVATION-IN-BIOME-CLASSIFICATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/drafts/2026-07-17/2026-07-17-MEDIUM-RESEARCH-ELEVATION-IN-BIOME-CLASSIFICATION.md \
         projects/galaxy_game/tasks/active/2026-07-17-MEDIUM-RESEARCH-ELEVATION-IN-BIOME-CLASSIFICATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-17-MEDIUM-RESEARCH-ELEVATION-IN-BIOME-CLASSIFICATION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-RESEARCH-ELEVATION-IN-BIOME-CLASSIFICATION.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# RESEARCH TASK: Does elevation currently feed into biome/temperature classification?

**Status**: DRAFT (not active — awaiting human review)  
**Priority**: MEDIUM  
**Type**: research  
**Created**: 2026-07-17  

---

## Objective

Determine whether the current terrain/biome generation pipeline applies an **elevation-based temperature adjustment** (lapse rate, roughly -6.5°C per km) when classifying biomes, or if biome assignment is driven solely by latitude/temperature with no elevation input.

This is **READ-ONLY** — report findings only, do not fix anything.

---

## Why This Matters

This blocks any biome-taxonomy or asset-variety work. The answer determines:

- Whether tropical-latitude high-elevation tiles should render as **alpine tundra** (if elevation matters) or **rainforest** (if it doesn't)
- Which biomes need art variants at all (e.g. if elevation creates new biome categories, the canonical tile set may need expansion)
- Whether `CanonicalMapService` needs additional elevation-aware aliases

---

## Research Scope

### Files to Investigate (READ ONLY — do not edit)

| File | What to Check |
|------|--------------|
| `galaxy_game/app/assets/javascripts/surface_view.js` | `_selectTerrainFamily()`, `_selectTerrainType()`, `_biomeTileKey()` — does elevation data influence any decision? |
| `galaxy_game/app/assets/javascripts/biome_renderer.js` | BIOME_NAMES constant, biome selection logic |
| Any Ruby terrain pipeline services | Look for files in `app/services/terra_sim/` or `app/services/ai_manager/` that compute biome from climate data |
| Climate/atmosphere models | Check if elevation is passed to any biome classification function |

### Specific Questions to Answer

1. **Does any code path use elevation data when classifying biomes?**
   - Search for: `elevation`, `altitude`, `height` in terrain/biome selection logic
   - If found: what lapse rate (if any) is applied?

2. **Is biome currently assigned from latitude/temperature alone?**
   - What climate variables feed into biome classification?
   - Is there a temperature threshold that varies by elevation?

3. **What data structures carry elevation information during rendering?**
   - Does `surface_view.js` have access to per-tile elevation?
   - If yes, is it used in any biome/terrain decision?

4. **Are there any existing elevation-based biome categories?**
   - e.g. "alpine", "montane", "highlands" — are these computed or hardcoded?

---

## Research Method

1. **Grep for elevation references** in terrain/biome code paths:
   ```bash
   grep -rn 'elevation\|altitude\|height' galaxy_game/app/assets/javascripts/surface_view.js galaxy_game/app/assets/javascripts/biome_renderer.js
   ```

2. **Trace the biome selection flow**:
   - Start from `_biomeTileKey()` or equivalent in `surface_view.js`
   - Follow the call chain to find what inputs determine the biome key
   - Check if elevation is among those inputs

3. **Check Ruby terrain pipeline** (if any):
   ```bash
   grep -rn 'elevation' galaxy_game/app/services/terra_sim/ | grep -i 'biome\|terrain\|climate'
   ```

4. **Report findings** — do NOT modify any code, config, or data files.

---

## Expected Output

A research report answering:

1. **Yes/No**: Does elevation currently influence biome classification?
2. **If Yes**: What lapse rate or formula is used? Which biomes are affected?
3. **If No**: What variables ARE used? (latitude, temperature, precipitation, etc.)
4. **Impact assessment**: How would adding elevation-based adjustment change the biome taxonomy?
5. **Files examined**: List of files read with line numbers where relevant logic was found

---

## Stop Conditions

- Report findings to chat when complete
- Do NOT modify any code or data files
- Do NOT create new services, models, or configurations
- Wait for human review before any follow-up work

---

## Dependencies

**Blocked by**: none (standalone research task)  
**Blocks**: Biome visual variety design (item 2), CanonicalMapService expansion if elevation-aware aliases are needed  
**Related tasks**: 
- `2026-07-17-HIGH-PHASE1-CANONICALMAPSERVICE.md` (CanonicalMapService — Phase 1 Task 1)
- Biome visual variety via deterministic tile variants (item 2 below)

---

## Completion Report Template
*Filled in by the researching agent after completion*

**Completed by**: [agent name]  
**Completion date**: YYYY-MM-DD  

### Findings Summary
[One-line answer: Yes or No — elevation feeds into biome classification]

### Detailed Findings
1. **Elevation influence**: [Yes/No + details]
2. **Variables used for biome classification**: [list]
3. **Lapse rate applied**: [value or "none"]
4. **Files examined**: [list with line numbers]

### Impact Assessment
[How this finding affects downstream work]

### Follow-up needed
[Any new research items identified — do not create the files, just list them here]

---

## Handoff Summary
HANDOFF SUMMARY: [files examined] | [findings: elevation influences biome? yes/no] | [next action needed]
