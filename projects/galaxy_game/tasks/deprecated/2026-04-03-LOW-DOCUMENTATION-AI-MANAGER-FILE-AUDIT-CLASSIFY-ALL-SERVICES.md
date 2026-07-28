---
name: "AI Manager File Audit"
priority: LOW
phase: phase8+
created: 2026-06-21
status: backlog
type: documentation/audit
---

# TASK: AI Manager File Audit — Read and Classify All Services

**Priority**: LOW  
**Phase**: phase8+ (Cycler Arc)  
**Type**: documentation/audit  
**Created**: 2026-06-21  

## Problem Statement

The `app/services/ai_manager/` directory contains approximately 83 files. A grep audit confirmed that only 5 files reference real application services (`CraftFactoryService`, `LaunchPaymentService`, `MissionTaskRunnerService`, `UnitLookupService`, `Market::Order`). The remaining ~78 files may contain real game logic, invented parallel logic, or duplicates of existing services.

**Current**: No classification exists for AI Manager service files  
**Expected**: Classification of all 83 files as CORE/LEGITIMATE/INVENTED/DUPLICATE with documentation

## Evidence of Incompleteness

```bash
ls galaxy_game/app/services/ai_manager/*.rb 2>/dev/null | wc -l
# Likely shows ~83 files needing classification
```

## Files to Review/Create

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/services/ai_manager/**/*.rb` (83 files) | AI Manager services | Read and classify each |
| `docs/architecture/ai_manager/service_classification.md` (new) | Classification documentation | CORE/LEGITIMATE/INVENTED/DUPLICATE |

## Acceptance Criteria

- [ ] All 83 AI Manager service files read and classified
- [ ] Classification: CORE (proven working), LEGITIMATE (real game logic), INVENTED (parallel logic), DUPLICATE (duplicates existing)
- [ ] Documentation of which files can be safely deleted vs kept
- [ ] No code changes — read-only audit only

## Implementation Steps

1. Read all 83 files in `app/services/ai_manager/` directory
2. Classify each file as:
   - **CORE**: Proven working (task_execution_engine.rb, state_analyzer.rb, resource_acquisition_service.rb, decision_tree.rb, system_architect.rb, manager.rb)
   - **LEGITIMATE**: Real game logic that should be kept
   - **INVENTED**: Parallel logic that duplicates existing services
   - **DUPLICATE**: Exact or near-duplicate of another file
3. Document classification in architecture docs
4. Identify files safe for deletion vs those requiring review

## Commit Instructions

```bash
git add docs/architecture/ai_manager/service_classification.md
git commit -m "docs: classify all AI Manager service files as CORE/LEGITIMATE/INVENTED/DUPLICATE"
```
