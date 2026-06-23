---
name: "AI Manager Align with SettlementDeploymentService"
priority: HIGH
phase: phase6+
created: 2026-06-21
status: backlog
type: architecture
---

# TASK: AI Manager — Strategically Align AI‑Managed Base Builds with SettlementDeploymentService

**Priority**: HIGH  
**Phase**: phase6+ (Tutorial Arc)  
**Type**: architecture/design  
**Created**: 2026-06-21  

## Problem Statement

`SettlementDeploymentService.establish_from_craft` is now the shared, canonical settlement‑deployment primitive used by `BaseSettlement` and player‑driven deployments. The AI‑managed generic base‑build pipeline (via `AutonomousConstructionManager` and `lib/tasks/generic_base_build.rake`) currently creates settlements via `BaseSettlement.create!` and helpers like `create_generic_settlement`, duplicating settlement‑creation logic.

**Current behavior**: AI Manager duplicates settlement creation logic, not aligned with canonical service  
**Expected behavior**: AI Manager uses `SettlementDeploymentService.establish_from_craft` + `tasks_v2` for consistent deployment

## Evidence of Incompleteness

```bash
grep -n "BaseSettlement.create\|create_generic_settlement" galaxy_game/app/services/ai_manager/**/*.rb 2>/dev/null | head -10
# Likely shows duplicated settlement creation logic
```

## Files to Review/Create

| File | Purpose | Key Section |
|------|---------|-------------|
| `galaxy_game/app/services/settlement_deployment_service.rb` | Canonical deployment service | establish_from_craft method |
| `galaxy_game/app/services/ai_manager/autonomous_construction_manager.rb` | AI construction logic | Settlement creation calls |
| `data/json-data/missions/tasks_v2/` | Reusable task library | Task composition patterns |

## Acceptance Criteria

- [ ] Design document shows how AI Manager should integrate with SettlementDeploymentService
- [ ] Recommendation includes tasks_v2 integration for consistent deployment logic
- [ ] Duplicated settlement creation paths identified and replaced in design
- [ ] Architecture aligns with AI_MANAGER_INTENT.md forbidden patterns (no direct model manipulation)

## Implementation Steps

1. Investigate current AI‑manager codebase for duplicated settlement creation logic
2. Design how AI Manager should integrate with SettlementDeploymentService.establish_from_craft
3. Produce concrete scoping-level recommendation for later implementation
4. Document integration plan with tasks_v2 reference patterns

## Commit Instructions

```bash
git add docs/architecture/ai_manager/settlement_deployment_alignment.md
git commit -m "docs: design AI Manager alignment with SettlementDeploymentService"
```
