---
status: backlog
priority: MEDIUM
type: feature
system_domain: AI_MANAGER
mvp_alignment: POST_LUNA_MULTI_SYSTEM
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear
- [ ] **Confirm blockers are actually cleared** (multi-system presence, acquisition layer, wormhole cost inputs)

**Task is NOT READY until all checkboxes are completed. Do not dispatch while Luna single-system work, Foothold Planner, or Material Acquisition are incomplete.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/ai-manager/2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md \
         projects/galaxy_game/tasks/active/2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-06-07-MEDIUM-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-06-07-FEATURE-MULTI-SYSTEM-RESOURCE-COORDINATION.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

---

# TASK: Multi-System Resource Coordination (Cross-Settlement Optimization)
**Status**: BACKLOG (DEFERRED — do not dispatch early)  
**Priority**: MEDIUM (effective priority: LOW until blockers clear)  
**Type**: feature  
**Created**: 2026-06-07  
**Last Updated**: 2026-09-03  

---

## Context

This is **network-scale AI Manager work**: after multiple settlements exist across the wormhole network, coordinate surplus/deficit, inter-settlement supply, and systemic risk — without inventing a second acquisition system.

### Layer stack (do not skip levels)

| Order | Layer | Role |
|-------|--------|------|
| 1 | Foothold Planner | Whether / how to establish presence on a body/system |
| 2 | Material sourcing & acquisition | Per need: market → harvest → depot → EAP |
| 3 | Local escalation / resupply | Single-settlement shortage and emergency rules |
| 4 | **This task** | Coordinate **across** settlements/systems once many nodes exist |

**Do not implement this as a replacement for per-settlement acquisition.** It sits on top of it.

### Prior art

- `docs/architecture/ai_manager/RESUPPLY_AND_ESCALATION_ARCHITECTURE.md`
- `docs/architecture/services/ai_manager/AI_MANAGER_CONSTRUCTION_ECONOMICS.md`
- Foothold Planner task (`2026-09-01-HIGH-ARCHITECTURE-RESOURCE-FIRST-FOOTHOLD-PLANNER.md`)
- Material sourcing & acquisition task (`2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md`)
- Wormhole / expansion design (controlled expansion, EAP, logistics as go-between, virtual ledger for DC–NPC paths)

### Origin

Extracted from archived April 2026 autonomous-expansion backlog (inter-system resource coordination). Older drafts mixed this with Luna-phase work. **This rewrite parks it correctly as post–single-system, post-foothold, post-acquisition.**

---

## Critical Information for This Task

### When this becomes relevant

Only after:

- Single-system supply chains are proven (Luna loop reliable)
- Foothold planning exists as a real path (not only named patterns)
- Per-settlement acquisition routing exists (market / harvest / EAP)
- More than one settlement (ideally more than one system) can actually trade
- Wormhole transit cost / connectivity are usable inputs (even if simplified)

Until then, keep this file in backlog and do not dispatch.

### Locked economic rules (inherit; do not reinvent)

1. Player-first market path with **real GCC** when price is acceptable and funds exist  
2. Self-harvest + list excess when market is too expensive and material is harvestable  
3. **EAP** = Earth cost + transport as expensive fallback; new local seeds at EAP × 0.9  
4. Preferred commerce: real GCC + **logistics company as go-between**  
5. Virtual ledger for DC–DC / many NPC–NPC only — not default player market path  
6. DCs prioritize **keeping settlements running** even at trade imbalance  
7. Normal shortage may wait for scheduled resupply; emergency follows escalation rules  
8. Network expansion is **controlled** — coordination optimizes what exists; it does not force growth

### Architecture Gotchas

⚠️ **GOTCHA 1: Not a second acquisition system**
- ❌ Wrong: Reimplement market/depot/EAP logic inside ResourceCoordinator
- ✅ Right: Call or assume per-settlement acquisition; this layer matches surplus nodes to deficit nodes and flags systemic risk
- Why: One design vision across layers

⚠️ **GOTCHA 2: Do not dispatch before blockers clear**
- ❌ Wrong: Build empire optimizer against Luna-only fixtures and call it done
- ✅ Right: Require multi-settlement (and ideally multi-system) scenarios and real cost inputs
- Why: Speculative APIs become permanent debt

⚠️ **GOTCHA 3: Wormhole costs are inputs, not a new physics engine**
- ❌ Wrong: Rebuild transit / retargeting inside this task
- ✅ Right: Consume documented wormhole transit cost and connectivity APIs
- Why: Stop condition if those APIs are missing — investigate separately

⚠️ **GOTCHA 4: Player economy stays intact**
- ❌ Wrong: Silent NPC-only rebalancing that starves player market opportunity
- ✅ Right: Prefer posting contracts / market-visible flows; NPC automation fills gaps per construction-economics rules
- Why: Player-first remains policy at network scale too

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

```markdown
## STATUS SYNTHESIS REPORT

**Task**: Multi-System Resource Coordination
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Implement cross-settlement surplus/deficit matching, ranked multi-source selection using wormhole cost inputs, inter-settlement trade when beneficial, and cascading-risk detection — on top of existing per-settlement acquisition, not instead of it.

### Blockers check (must all be true)
- [ ] Single-system loop proven enough to trust logistics primitives
- [ ] Foothold Planner architecture landed
- [ ] Material acquisition routing contract exists
- [ ] ≥2 settlements (preferably cross-system) available for scenarios
- [ ] Wormhole transit/connectivity cost inputs usable

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| shortage_detector / import_request_generator | Local foundations | pending |
| Acquisition / Procurement path | Per-need routing | pending |
| wormhole architecture docs | Transit cost inputs | pending |
| construction economics / escalation | Economic policy | pending |

### Expected Outcomes
- ResourceCoordinator (or equivalent) with clear public API
- Ranked sources for a deficit including transit cost
- Inter-settlement trade only when beneficial vs local options
- Cascading risk flags for single points of failure
- Specs with multi-settlement factories
- No regression of single-settlement import behavior

### Critical Gotchas I Will Avoid
- ❌ Parallel acquisition system — instead ✅ layer on existing
- ❌ Infinite-capacity wormhole assumptions without documenting them
- ❌ Ignoring player-first / GCC / EAP rules

---
**SYNTHESIS COMPLETE.** Ready to proceed.
```

---

## Problem Statement

Single-settlement shortage detection and Earth→local import paths are not enough once many settlements exist across a wormhole network.

**Missing:**

- Match surplus capacity at one node to deficit at another with transit-aware cost  
- Automate inter-settlement supply when it beats pure local production or EAP import  
- Detect systemic risk (one producer critical to many consumers)  
- Balance production targets with export/reserve needs without starving locals  

**Current:** Local shortage → local/import logic.  
**Expected:** Network-aware coordination that respects the same economic rules as local acquisition.

---

## Files Involved

### Primary (when dispatched)

| File | Purpose |
|---|---|
| `app/services/ai_manager/resource_coordinator.rb` (new) | Cross-settlement optimization API |
| `spec/services/ai_manager/resource_coordinator_spec.rb` (new) | Multi-settlement scenarios |
| Light integration with existing logistics import/shortage services | Multi-source selection hook — do not gut single-settlement paths |

### Reference (read only)

| File | Why |
|---|---|
| `app/services/logistics/shortage_detector.rb` | Local pattern to extend, not replace |
| `app/services/logistics/import_request_generator.rb` | Single-source evolution point |
| Wormhole architecture docs | Transit cost, capacity, stability inputs |
| Acquisition / Foothold / escalation / construction economics docs | Policy |

### Migration

- [ ] **No new schema required** for an initial version (use existing settlements, inventories, contracts)

---

## Implementation Steps (when blockers are clear)

1. Confirm blockers (synthesis checklist). Stop if any fail.  
2. Define API for:
   - `optimize_resource_flow` / equivalent — surplus and deficit sets → suggested routes  
   - `select_optimal_sources` — ranked sources with transit + capacity + stability factors  
   - `detect_cascading_risk` — single points of failure / no backup supply  
3. Implement matching that **calls or respects** per-settlement acquisition rules (do not duplicate market/GCC/harvest/EAP logic).  
4. Create inter-settlement trade/contracts only when total system impact improves vs local production or EAP path.  
5. Prefer market-visible / contract flows consistent with player-first policy.  
6. Specs: multi-settlement factories; ranking includes transit cost; no regression on single-settlement import.  
7. Document assumptions (e.g. simplified wormhole capacity) as TODOs, not hidden behavior.

---

## Acceptance Criteria

- [ ] Blockers were verified clear before implementation  
- [ ] Coordinator returns actionable surplus→deficit routing suggestions  
- [ ] Source ranking includes wormhole transit cost (and stability if available)  
- [ ] Inter-settlement trade only when beneficial vs local/EAP options  
- [ ] Cascading risk detection flags critical single-supplier patterns  
- [ ] Does not reimplement material JSON sourcing or full acquisition decision tree  
- [ ] Player-first / GCC / EAP / logistics-go-between rules are respected in design  
- [ ] Focused RSpec coverage; no regression of existing single-settlement import behavior  
- [ ] Out of scope items remain out of scope  

---

## Out of Scope

- TerraGen consortium shared pools  
- Eden-specific prioritization special cases  
- Player mission generation from cross-system shortages (separate, later)  
- Full real-time ship position optimization  
- Rebuilding wormhole physics or retargeting  
- Foothold Planner or per-settlement acquisition implementation  

---

## Dependencies

**Blocked by:**

- Luna / single-system supply chain reliability sufficient to trust logistics primitives  
- `2026-09-01-HIGH-ARCHITECTURE-RESOURCE-FIRST-FOOTHOLD-PLANNER.md` (architecture landed)  
- `2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md` (acquisition contract exists)  
- Usable wormhole connectivity / transit cost inputs  
- Practical multi-settlement (ideally multi-system) test scenarios  

**Blocks:** Higher-level network mastery features, consortium-style shared logistics (later)

**Related:** Wormhole expansion / Hammer / archive-reload design; construction economics; escalation

---

## Stop Conditions

- Wormhole transit cost APIs undocumented or missing → investigation task first, do not invent  
- Requires large core logistics rewrite beyond a thin multi-source hook  
- Cannot build honest multi-settlement scenarios  
- Design pressure to bypass player market entirely with invisible NPC balancing  

---

## Notes

- Older extractions labeled this Phase 5+ / Act 4 / post-MVP — that timing is still right.  
- Controlled expansion policy: this coordinator optimizes **existing** network load; it does not justify opening systems the AI Manager has not decided to keep.  
- When infrastructure (depots, cyclers) changes transport cost, EAP and cross-system rankings should move together — same economic spine as local acquisition.
