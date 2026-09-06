---
status: backlog
priority: MEDIUM
type: architecture
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
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

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/ai-manager/2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/ai-manager/2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md \
         projects/galaxy_game/tasks/active/2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-09-03-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

---

# TASK: Material Sourcing & Acquisition Logic (Architecture + Routing Contract)
**Status**: BACKLOG  
**Priority**: MEDIUM  
**Type**: architecture  
**Created**: 2026-09-03  
**Last Updated**: 2026-09-03  

---

## Context

This task defines how the AI Manager acquires materials at need-time. It is the **acquisition layer under** the Resource-First Foothold Planner — not a rewrite of foothold planning and not a place to embed sourcing lists in material JSON.

### Prior art (read first)

1. **`docs/architecture/ai_manager/RESUPPLY_AND_ESCALATION_ARCHITECTURE.md`**  
   State-based decisions; ISRU-first even in crisis; escalation is exception handler, not the default.

2. **`docs/architecture/services/ai_manager/AI_MANAGER_CONSTRUCTION_ECONOMICS.md`**  
   Local resources + robot labor → player contracts → NPC import last.  
   Local extraction cost vs import EAP; player-first; DC continuity over short-term balance.

3. **Resource-First Foothold Planner** (`2026-09-01-HIGH-ARCHITECTURE-RESOURCE-FIRST-FOOTHOLD-PLANNER.md`)  
   Body/system → ranked foothold options. This task answers: once a plan needs material X, how do we get it?

4. **Canonical material pattern** — facility-based, not location-keyed (see epoxy_resin shape below).

### Anti-pattern (forbidden)

Material JSON must **not** carry location-keyed sourcing blocks:

```json
"sourcing": {
  "lunar": { "availability": "very_high", "in_situ": true },
  "martian": { ... },
  "earth": { ... }
}
```

That pattern does not scale to Eden or procedural worlds. Runtime investigation found such blocks unread by code anyway. **Materials are passive** (what + how to produce + baseline economics). **Routing is active** (AI Manager / Procurement at need-time).

### Canonical material shape (target)

```json
{
  "production": {
    "facility_type": "chemical_synthesis_plant",
    "input_materials": [],
    "energy_kwh_per_kg": 8.5,
    "production_time_hours": 0.5
  },
  "cost_data": {
    "purchase_cost": { "amount": 10000 },
    "import_config": { "transport_category": "standard" }
  },
  "pricing": {
    "local_production": {
      "facility_required": "chemical_synthesis_plant",
      "cost_per_kg": 7500,
      "energy_kwh_per_kg": 8.5
    }
  }
}
```

- No per-body sourcing keys  
- Earth baseline in `cost_data.purchase_cost`  
- Local production economics via facility requirement (works anywhere the facility exists)  
- Note: prefer `local_production` (or equivalent facility-based key) over a body-named key like `lunar_production` in new work

---

## Critical Information for This Task

### Locked economic rules (do not contradict)

1. **EAP** = Earth cost + Transport Cost — expensive fallback, always computable.  
2. **New local listings without market history** seed at **EAP × 0.9** (undercut import).  
3. **Player-first market buy** when price is acceptable **and** the DC has **real GCC** on hand.  
4. If local price is too high **and** the material is **harvestable** → deploy robotic fleets, take what is needed, **list excess on the market**, continue.  
5. **Normal shortage** → prefer wait for scheduled cycler/resupply over paying sustained gouge prices (unless emergency rules fire).  
6. **Preferred commerce path** = real GCC market transactions with a **logistics company as go-between**.  
7. **Virtual ledger** = DC–DC and many NPC–NPC only; not the default for ordinary player-facing market buys. Real GCC preferred when it applies.  
8. **DC priority** = keep the settlement running even at a trade imbalance; continuity beats neat short-term books.  
9. As orbital infrastructure improves (LEO depot, cyclers), **transport cost adjusts** and EAP/local seeds move with it.

### Architecture Gotchas

⚠️ **GOTCHA 1: Materials do not own routing**
- ❌ Wrong: Add `sourcing.lunar/martian/earth` (or similar) to material JSON  
- ✅ Right: Recipe + facility + baseline economics in JSON; route at need-time in Procurement / AI Manager  
- Why: Same material must work on unknown procedural worlds

⚠️ **GOTCHA 2: Do not implement full Foothold Planner here**
- ❌ Wrong: Expand this into body/system foothold ranking  
- ✅ Right: Acquisition routing contract for “we need material X”  
- Why: Foothold Planner is a separate, higher-level task

⚠️ **GOTCHA 3: GCC gate and player-first are mandatory**
- ❌ Wrong: Always auto-buy or always self-produce  
- ✅ Right: Market buy only if price sane **and** real GCC available; else harvest / wait / import per rules above  
- Why: Player economy and budget realism

⚠️ **GOTCHA 4: Scope is architecture + routing contract (+ minimal hook), not full economy rewrite**
- ❌ Wrong: Rebuild cycler transit engine, full depot system, or all material JSON in one pass  
- ✅ Right: Decision tree, cost comparison contract, integration point on ProcurementService (or equivalent), one clear test case  
- Why: Keep dispatchable and reviewable

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

```markdown
## STATUS SYNTHESIS REPORT

**Task**: Material Sourcing & Acquisition Logic
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Define acquisition routing for material needs: market → depot → local facility production → EAP import, with player-first, GCC gate, self-harvest+list excess, and no location-keyed material sourcing.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| RESUPPLY_AND_ESCALATION_ARCHITECTURE.md | Prior art | pending |
| AI_MANAGER_CONSTRUCTION_ECONOMICS.md | Cost / player-first prior art | pending |
| Foothold Planner task | Upstream dependency | pending |
| epoxy_resin (or canonical material shape) | Material pattern | pending |
| ProcurementService (or equivalent) | Integration point | pending |

### Prerequisites Completed
- ✅ Step 0 done
- ✅ Read prior art and locked economic rules
- ✅ Understand the four Gotchas

### Expected Outcomes
- Written decision tree matching locked rules
- Cost comparison returns ranked route options (not only yes/no)
- Clear integration point
- No location-keyed sourcing in material design
- Explicit non-goals

### Critical Gotchas I Will Avoid
- ❌ Sourcing blocks on materials — instead ✅ facility + baseline economics only
- ❌ Ignoring GCC gate / player-first — instead ✅ enforce both
- ❌ Full foothold or transport rewrite — instead ✅ acquisition contract only

---
**SYNTHESIS COMPLETE.** Ready to proceed.
```

---

## Problem Statement

Acquisition was at risk of being modeled as location-keyed data on materials. The correct model is runtime routing driven by facility capability, market state, GCC, harvestability, depots, and EAP.

**Current risk**: Dead `sourcing.lunar/martian/earth` patterns; unclear AI Manager path when a base needs a material.  
**Expected behavior**: Facility-based material data + explicit need-time decision tree with ranked cost/time options.

---

## Files Involved

### Primary
| File | Purpose |
|---|---|
| `app/services/ai_manager/` ProcurementService or equivalent (new or existing) | Acquisition routing entry |
| Short design note under `docs/architecture/ai_manager/` (optional) | Capture decision tree + EAP rules |

### Reference
| File | Why |
|---|---|
| Prior-art docs listed above | Grounding |
| Canonical manufactured material JSON (epoxy_resin path when available) | Pattern |
| Foothold Planner task / skeleton | Upstream; do not merge scopes |

### Data audit (non-blocking)
| Area | Action |
|---|---|
| `data/json-data/resources/materials/processed/` | Document files still using location-keyed sourcing; refactor list or defer with justification — do not block architecture on full audit |

---

## Implementation Steps

1. Read prior art and this task’s locked economic rules.  
2. Write the **authoritative decision tree** for “settlement needs material X” (include all locked rules).  
3. Define **route option output**: 2–3 options with cost, time, and source type (market / local production / depot / EAP import / self-harvest).  
4. Define **cost model hooks**:  
   - Earth baseline ← `cost_data.purchase_cost` (+ transport category)  
   - EAP ← Earth cost + current transport cost  
   - Local production ← facility present ? production cost + inputs : unavailable  
   - New local list seed ← EAP × 0.9 when no history  
5. Identify **integration point** (`ProcurementService` or equivalent): input material id + settlement context → ranked routes.  
6. Specify **self-harvest branch**: harvestable + (price too high or no GCC) → produce for need, list excess.  
7. Specify **emergency vs normal**: normal may wait for cycler; emergency may accept higher cost / urgency premium (align with escalation design; do not invent a parallel emergency system).  
8. Document **non-goals**: full material JSON migration, full cycler engine, full depot simulation, foothold ranking, live-loop wiring.  
9. One **test case design**: need epoxy_resin (or stand-in); with facility → local route preferred; without → market/depot/EAP options; no location-keyed fields required.

---

## Acceptance Criteria

- [ ] Decision tree includes: market + GCC gate, self-harvest + list excess, depot check, local facility production, EAP import, emergency vs normal  
- [ ] Materials described as facility + baseline economics only — no location-keyed sourcing in the design  
- [ ] EAP and EAP × 0.9 seed for new local listings are documented  
- [ ] Preferred path (real GCC market + logistics go-between) and virtual-ledger scope (DC/NPC) are documented  
- [ ] DC continuity (run even at trade imbalance) is acknowledged in prioritization notes  
- [ ] Integration point on Procurement (or equivalent) is named  
- [ ] Output is ranked route options with cost/time, not only boolean  
- [ ] Non-goals explicit; full JSON audit may be deferred with a short list  
- [ ] Does not reimplement Foothold Planner or cycler transit engine  

---

## Dependencies

**Blocked by**: Resource-First Foothold Planner architecture task should land first (contract + skeleton) so acquisition stays the layer underneath  
**Blocks**: Clean runtime “get material X” behavior for settlements; consistent pricing seeds  
**Related**: Foothold Planner; escalation architecture; construction economics; material JSON hygiene (audit)

---

## Stop Conditions

- Requires rewriting all material JSON before the routing contract exists  
- Turns into full transport/depot/cycler implementation  
- Merges into Foothold Planner implementation scope  
- Reintroduces location-keyed sourcing on materials  

---

## Clarifications (resolved for this rewrite)

| Topic | Resolution |
|-------|------------|
| Depot routing | Interface: “check depot availability”; deep depot logistics can remain transport-owned |
| Cycler times | Use as **inputs** to cost/time options; do not rebuild transit engine here |
| Urgency premium | Allowed on emergency path; normal path stays near EAP / scheduled resupply |
| “Build this facility” | Logic may recommend when imports consistently lose to local ROI; full commander UX can be follow-up |
