---
status: backlog
priority: MEDIUM
type: feature
system_domain: AI_MANAGER
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase8+/2026-04-17-ADVANCED-CLAUDE-OPTIMIZE-MISSION-PLANS-AI-TRAINING.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase8+/2026-04-17-ADVANCED-CLAUDE-OPTIMIZE-MISSION-PLANS-AI-TRAINING.md \
         projects/galaxy_game/tasks/active/2026-04-17-ADVANCED-CLAUDE-OPTIMIZE-MISSION-PLANS-AI-TRAINING.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-04-17-ADVANCED-CLAUDE-OPTIMIZE-MISSION-PLANS-AI-TRAINING.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Optimize Mission Plans for AI Training and Gameplay
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-06-21
**Last Updated**: 2026-08-07

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model during backlog review*

- **Template Conformance**: PASS — updated to current template 2026-08-07
- **Docker Wrapper Check**: N/A — data/template work, no RSpec strings needed
- **MVP Alignment**: STALE — phase8+ priority, not Luna MVP scope
- **MVP Impact Note**: Mission plan templates feed AI training pipeline (Phase 9+)
- **Action Line**: NEEDS MANUAL REVIEW — phase8+ priority, deferred until Luna MVP complete

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Escalation Path**: Cloud fallback after two local failures

---

## Prerequisites

1. Review existing AI training data:
   ```bash
   ls -la galaxy_game/data/json-data/ai_manager/
   cat galaxy_game/data/json-data/ai_manager/learned_patterns.json | head -50
   ```
2. Check current mission template structure:
   ```bash
   find galaxy_game/data/json-data/missions/templates/ -name "*.json" 2>/dev/null
   ```
3. Review PHASE_STRUCTURE.md for phase8+ context

---

## Architecture Gotchas

- **Do NOT** modify existing mission data files — only create new standardized templates
- Templates go in `data/json-data/missions/templates/` (create if needed)
- Economic/risk metadata must follow consistent schema across all templates
- AI training hooks must reference `learned_patterns.json` by pattern ID, not file path

---

## REQUIRED: Synthesis Report

**Before starting any work**, agent must:
1. Read `data/json-data/ai_manager/learned_patterns.json` completely
2. Audit existing mission template files for metadata gaps
3. Identify which template types are needed (orbital_establishment, resource_extraction, industrial_hub, wormhole_exploitation)
4. Save synthesis report to `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/YYYY-MM-DD-MISSION-TEMPLATES-SYNTHESIS.md`
5. Report findings in chat before proceeding

---

## Problem Statement

The AI Manager has generated actionable training data and pattern learnings (see data/json-data/ai_manager/). Mission plans currently lack direct integration of these learnings, and metadata is inconsistent or incomplete. Recent codebase stabilization paused advanced AI integration; this task resumes that work.

**Current**: Mission plans lack AI training data integration, inconsistent metadata  
**Expected**: Standardized mission templates with economic/risk/dependency metadata and AI learning hooks

## Evidence of Incompleteness

```bash
grep -n "economic_metadata\|ai_manager_integration" data/json-data/missions/**/*.json 2>/dev/null | head -5
# Likely returns no results — metadata blocks not integrated
```

## Files to Edit/Create

| File | Purpose | Key Section |
|------|---------|-------------|
| `data/json-data/missions/templates/orbital_establishment_template.json` (new) | Standardized template | Economic/risk metadata |
| `data/json-data/missions/templates/resource_extraction_template.json` (new) | Standardized template | AI training hooks |
| `data/json-data/ai_manager/learned_patterns.json` | Existing patterns | Integration references |

## Acceptance Criteria

- [ ] Standardized mission templates created with required metadata blocks
- [ ] Economic gradient data integrated in all missions (roi_estimate, risk_multiplier, dependency_value)
- [ ] Risk assessment framework implemented (technical_risk, environmental_risk, economic_risk, strategic_risk)
- [ ] Inter-mission dependency declarations added (prerequisites, enablers, blocks, parallels)
- [ ] AI training data hooks and pattern references integrated in mission plans

## Implementation Steps

1. **Mission Template Standardization**
   - Create/verify standardized mission templates for common patterns:
     - orbital_establishment_template.json
     - resource_extraction_template.json
     - industrial_hub_template.json
     - wormhole_exploitation_template.json
   - Ensure all templates include required metadata blocks (economic, risk, dependency, AI training)
2. **Economic Metadata Enhancement**
   - Add/verify economic gradient data in all missions
3. **Risk Assessment Framework**
   - Implement standardized risk categories in all missions
4. **Dependency Mapping**
   - Add/verify inter-mission dependency declarations
5. **AI Manager Learning Integration**
   - Add/verify AI training data hooks and direct references to learned patterns

## Commit Instructions

```bash
git add data/json-data/missions/templates/*.json data/json-data/ai_manager/learned_patterns.json
git commit -m "feat: standardize mission templates with AI training metadata"
```
