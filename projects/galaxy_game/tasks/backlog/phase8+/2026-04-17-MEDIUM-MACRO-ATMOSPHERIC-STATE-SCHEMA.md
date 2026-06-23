---
name: "Define Atmospheric State Schema"
priority: MEDIUM
phase: phase8+
created: 2026-06-21
status: backlog
type: schema
---

# TASK: Define Canonical Atmospheric State Schema

**Priority**: MEDIUM  
**Phase**: phase8+ (Cycler Arc)  
**Type**: schema definition / integration  
**Created**: 2026-06-21  

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
