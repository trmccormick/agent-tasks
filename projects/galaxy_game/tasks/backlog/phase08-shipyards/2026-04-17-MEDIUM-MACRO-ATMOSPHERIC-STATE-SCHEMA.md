---
status: backlog
priority: MEDIUM
type: schema
system_domain: TERRA_SIM
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase8+/2026-04-17-MEDIUM-MACRO-ATMOSPHERIC-STATE-SCHEMA.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/phase8+/2026-04-17-MEDIUM-MACRO-ATMOSPHERIC-STATE-SCHEMA.md \
         projects/galaxy_game/tasks/active/2026-04-17-MEDIUM-MACRO-ATMOSPHERIC-STATE-SCHEMA.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-04-17-MEDIUM-MACRO-ATMOSPHERIC-STATE-SCHEMA.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Define Canonical Atmospheric State Schema
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: schema
**Created**: 2026-06-21
**Last Updated**: 2026-08-07

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model during backlog review*

- **Template Conformance**: PASS — updated to current template 2026-08-07
- **Docker Wrapper Check**: N/A — schema/data work, no RSpec strings needed
- **MVP Alignment**: STALE — phase8+ priority, not Luna MVP scope
- **MVP Impact Note**: Atmospheric schema feeds TerraSim/Digital Twin Sandbox (Phase 9+)
- **Action Line**: NEEDS MANUAL REVIEW — phase8+ priority, deferred until Luna MVP complete

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Escalation Path**: Cloud fallback after two local failures

---

## Prerequisites

1. Verify current schema directory state:
   ```bash
   ls -la galaxy_game/data/schemas/
   cat galaxy_game/data/schemas/component_blueprint_v1.1.json | head -20
   ```
2. Check if atmospheric services exist (they don't — task references them as integration targets):
   ```bash
   find galaxy_game/app/services/ai_manager -name "*atmospheric*" -o -name "*stabilization*" 2>/dev/null
   ```
3. Review existing schema format from component_blueprint_v1.1.json for consistency
4. Review PHASE_STRUCTURE.md for phase8+ context

---

## Architecture Gotchas

- **Do NOT** create atmospheric_evaluator.rb or stabilization_planner.rb — these are future services; this task only creates the schema
- Schema goes in `data/schemas/atmospheric_state.schema.json` (create directory if needed)
- Follow JSON Schema draft-07 format consistent with existing schemas (component_blueprint_v1.1.json)
- Schema should support: seasonal modifiers, dust storm triggers, resource monitoring, event-driven simulation logic
- Document integration points in GUARDRAILS.md and architecture docs after creation

---

## REQUIRED: Synthesis Report

**Before starting any work**, agent must:
1. Read `data/schemas/component_blueprint_v1.1.json` for schema format reference
2. Audit existing atmospheric services (atmospheric_evaluator.rb, stabilization_planner.rb) — note they don't exist yet
3. Identify which atmospheric state fields are needed based on completed atmospheric tasks (extraction, transfer/venting, loss/solar wind)
4. Save synthesis report to `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/YYYY-MM-DD-ATMOSPHERIC-SCHEMA-SYNTHESIS.md`
5. Report findings in chat before proceeding

---

## Problem Statement

No canonical JSON schema exists for planetary atmospheric state tracking. This is a foundational requirement for the Digital Twin Sandbox, TerraSim, and all AI/automation modules that serialize, validate, or exchange atmospheric state data.

**Current**: No atmospheric_state.schema.json exists  
**Expected**: Canonical JSON schema supporting seasonal modifiers, dust storm triggers, resource monitoring, and event-driven simulation logic

## Evidence of Incompleteness

```bash
ls data/schemas/atmospheric_state.schema.json 2>/dev/null
# Likely returns no results — schema file doesn't exist
```

## Files to Create/Modify

| File | Purpose | Key Section |
|------|---------|-------------|
| `data/schemas/atmospheric_state.schema.json` (new) | Canonical atmospheric state schema | JSON Schema definition |
| `galaxy_game/app/services/ai_manager/atmospheric_evaluator.rb` | AI evaluation | Schema validation integration |
| `galaxy_game/app/services/ai_manager/stabilization_planner.rb` | Stabilization planning | Schema validation integration |

## Acceptance Criteria

- [ ] Canonical JSON schema for atmospheric state defined and validated
- [ ] Integrated with AI Manager, TerraSim, and Digital Twin Sandbox
- [ ] RSpec coverage for schema validation and integration
- [ ] Supports event triggers (dust storms, seasonal modifiers, resource monitoring)
- [ ] Schema documented in GUARDRAILS.md and architecture docs

## Implementation Steps

1. Draft canonical JSON schema for atmospheric state (data/schemas/atmospheric_state.schema.json)
2. Integrate schema validation with AI Manager, TerraSim, and Digital Twin Sandbox
3. Write/extend RSpec for schema validation and integration
4. Document schema and integration points in GUARDRAILS.md and architecture docs

## Commit Instructions

```bash
git add data/schemas/atmospheric_state.schema.json galaxy_game/app/services/ai_manager/atmospheric_evaluator.rb
git commit -m "feat: add canonical atmospheric state schema for AI Manager and TerraSim"
```
