---
status: backlog
priority: HIGH
type: feature
system_domain: AI_MANAGER | TERRA_SIM | LOGISTICS
mvp_alignment: LUNA_BASE_OPERATIONS
local_worker_safe: true
---

# TASK: Luna Daily Tick Loop — Base Operations Simulation

**Assigned to**: Qwen (local worker, standard supervision)
**Status**: backlog
**Priority**: HIGH
**Type**: feature
**Created**: 2026-07-21
**Last Updated**: 2026-07-21

---

## ⚡ Minimal Handoff

**Build a standalone daily-tick simulation rake** that advances a deployed Luna base forward N days (default: 30), tracking inventory deltas and making local-vs-import decisions each tick.

**This is NEW, SEPARATE rake** — not an extension of `luna_mission.rake` (that's an integration test harness, locked per project convention).

**MVP scope only**: Three components — daily state advancement, inventory delta calc, binary import gate. Stop and escalate if scope creeps beyond this.

**Key dependency**: `PrecursorCapabilityService.can_produce_locally?` (just fixed, 2026-07-19). Use it; don't reimplement.

---

## Context

### The Gap
Nothing in the codebase simulates a settlement **running over time with production cycles**. Everything existing is one-shot:
- `luna_mission.rake` — integration test harness (validates deployment only)
- `lunar_precursor_mission_validation.rake` — deployment validation (all 7 phases one-shot)
- `venus_mars:pipeline_v2` — **reference pattern only** (10,000-year time-loop with yearly conditional updates + state persistence; reuse architecture, not time scale)

This task fills that gap: post-deployment base operation (production ticks, stockpile accumulation, import-vs-local decisions over 30-day window).

### MVP Scope
**IN**: Earth→Luna imports, local regolith I-beam production, ISRU operation, L1/LEO material flow tracking
**OUT**: Venus/Titan routes, full economic optimization, AI learning/adaptation, multi-resource optimization

---

## Problem Statement

Deployed Luna base (from `luna_mission.rake`) has:
- Active population (consumes O2, water, food daily)
- Deployed ISRU infrastructure (produces O2, H2 from regolith)
- Deployable production units (I-beam 3D printer, solar panels — some local, some import-gated)
- Base maintenance requirements (solar array: 8760-hour (annual) maintenance intervals with consumable tasks)

**Current state**: No simulation of what happens next. We don't know:
- Does stockpile grow or shrink?
- When do imports become necessary?
- What's the decision logic for "import vs. local production"?

**This task**: Advance the base forward N days, track inventory changes, log import decisions.

---

## Architecture

### Time-Loop Pattern (Reference: venus_mars:pipeline_v2)
The existing `venus_mars:pipeline_v2` rake demonstrates the pattern — reuse its **structure**, not its scale:

```
ARCHITECTURE (from venus_mars:pipeline_v2):
  while current_day < total_days:
    - Phase Check: preconditions met? (e.g., is feedstock available?)
    - Resource Harvest: ISRU production (O2, H2, etc.)
    - Processing/Loss Application: consumption (life support, maintenance, production)
    - State Persistence: save deltas to settlement
    - Conditional Gates: yearly updates (e.g., magnetosphere deployment)
```

For Luna, translate to:
```
DAILY TICK LOOP (30 days, MVP scope):
  while current_day < 30:
    1. CHECK local availability (PrecursorCapabilityService.can_produce_locally?)
    2. PRODUCE daily ISRU output (O2, H2)
    3. CONSUME three tiers: life support + blueprint production + base maintenance
    4. CALCULATE delta (production - consumption)
    5. EVALUATE import gate: is projected stockpile exhaustion coming?
    6. LOG decision: [Decision: IMPORT_O2] or [Decision: LOCAL_ONLY]
    7. PERSIST state to settlement.inventory
```

---

## Consumption Model — Three Tiers

### Tier A: Human Crew Life Support (Hard Requirement)
**Cannot be paused.** Fixed daily rates per active population from `GameConstants::HUMAN_LIFE_SUPPORT`:
- O2: 0.84 kg/person/day
- Water (drinking): 2.5 L/person/day
- Water (all uses): 50 L/person/day
- Food: 2 kg/person (if stockpile drops to STARVATION_THRESHOLD = 0.5 kg/person, population declines)

**Per tick**: `life_support_consumption = active_population × HUMAN_LIFE_SUPPORT rates`

### Tier B: Blueprint Production (Blocked if Materials Unavailable)
**Jobs cannot be submitted if materials are missing** — zero consumption until materials arrive.

- **I-beams** (regolith → 3D printed): Regolith available locally on Luna → effectively unlimited production (limited by printer availability, not feedstock). Read from `3d_printed_ibeam_mk1_bp.json`: 75 kg regolith → 69 kg I-beam, 2 hr production.
- **Solar cover panels** (6 specialized materials): NOT producible locally on Luna (advanced_solar_cells, graphene_layers, transparent_conducting_oxide, reinforced_aluminum_frame, protective_glass_coating, power_conditioning_electronics required). Production is **import-gated**.

**Per tick**: If materials available and job submitted, consume feedstock from inventory. If materials missing, job queued but not started — zero consumption for that resource.

### Tier C: Base Maintenance (Periodic Infrastructure Drain)
**Consumes materials periodically** to keep deployed infrastructure operational.

- Solar panel array: `scheduled_maintenance_interval_hours: 8760` (1 year), tasks: surface_cleaning, electrical_connections_check, tracking_calibration, efficiency_measurement
- Maintenance materials: cleaning supplies, replacement parts, etc. (exact draw rates per maintenance task in operational_data JSON)

**Per tick**: If deployed unit has pending maintenance due, consume maintenance materials. If no materials available, infrastructure degrades (flag for user decision later — MVP doesn't auto-trigger repair import).

---

## Implementation Requirements

### 1. Settlement Model — Tick Tracking Fields
Add to `BaseSettlement` (or equivalent settlement model):
- `tick_count` (integer) — number of completed daily ticks (for tracking progress)
- `last_simulated_at` (datetime) — timestamp of last tick execution
- (Other fields TBD by implementing agent based on existing schema — keep **additive only**, no breaking changes)

### 2. Daily Tick Service
Lightweight service class, rake-callable, executes fixed N daily steps (default `n = 30`, configurable).

**Input**: Already-deployed settlement (run `luna_mission.rake` first to create it)

**Per tick**:
- Calculate production: delegate to `AIManager::PrecursorCapabilityService` for ISRU output
- Calculate consumption:
  - **Tier A (life support)**: `active_population × GameConstants::HUMAN_LIFE_SUPPORT rates`
  - **Tier B (production)**: if materials available, submit job; if not, zero
  - **Tier C (maintenance)**: read from deployed unit's `operational_data.maintenance_requirements`, consume if due
- Calculate delta: `delta = production - consumption`
- Apply delta to `settlement.inventory`
- Evaluate import gate (see next section)
- Log decision
- Persist state

### 3. Import Decision Gate
Per tick, per tracked resource, decide: **import or local only?**

**Logic**:
1. Call `PrecursorCapabilityService.can_produce_locally?(resource)` — returns true/false
2. If true: rely on local ISRU, no import
3. If false: check projected stockpile exhaustion:
   - Estimate daily consumption rate for this resource
   - Project: `days_until_exhaustion = current_stockpile / daily_consumption_rate`
   - Compare: `transit_time = Logistics::Contract::EARTH_LUNA_TRANSIT_DAYS` (currently hardcoded as 3)
   - If `days_until_exhaustion < transit_time`: trigger import (log decision, don't execute cargo scheduling for MVP)
   - Else: continue on current stockpile

**Import cost data**: `Logistics::TransportCostService.calculate_cost_per_kg(from:, to:, resource:)` **exists and is callable**. It returns a single float (GCC/kg) for Earth→Luna routes. For MVP, use it directly — no stubbing needed.

**Current limitations** (documented in the service itself):
- `in_situ_refueling_available?` is currently a **stub returning false** — this means all costs are baseline (no refueling discounts). This is intentional; infrastructure state will be wired later.
- Route modifiers come from `EconomicConfig.route_modifier(route_key)` — if a route isn't configured, it falls back to physics-based calculation via `RouteCostCalculator`.
- Resource category detection (`determine_category`) classifies resources as `bulk_material`, `manufactured`, or `high_tech` for rate selection.

**If data doesn't exist for a resource**: The service always returns *something* (either configured cost or physics-based fallback). If the result seems wrong, log it with `[Decision: COST_UNVERIFIED]` rather than treating it as a gap — the method exists, just may need EconomicConfig tuning later.

**Decision logging format**: Match existing patterns (e.g., emission logging in atmosphere feedback). Examples:
```
[Decision: IMPORT_O2] — projected exhaustion before Earth→Luna transit (3d)
[Decision: LOCAL_ONLY] — PrecursorCapabilityService confirms local production available
[Decision: IMPORT_GAP] — resource not in TransportCostService; cannot calculate cost
```

### 4. Rake Entry Point
New task file, e.g., `lib/tasks/luna_operations_simulation.rake` (separate from `luna_mission.rake`).

**Namespace**: `luna:simulate_operations` (confirm no collision with existing rakes).

**Usage**:
```bash
# Run 30 days (default)
bundle exec rake luna:simulate_operations

# Run custom day count
bundle exec rake luna:simulate_operations[60]
```

---

## Stop Conditions — Escalate Immediately

Do NOT proceed without flagging if:

1. **PrecursorCapabilityService gap**: `can_produce_locally?` doesn't cover a resource this loop needs → stop, escalate
2. **Import cost data**: `TransportCostService.calculate_cost_per_kg` **always returns a value** (configured cost or physics-based fallback). If the result seems inaccurate, log with `[Decision: COST_UNVERIFIED]` — do NOT stop. The service exists and is callable; EconomicConfig tuning is post-MVP work.
3. **Settlement schema issue**: Settlement or inventory models require **breaking changes** to support tick fields → stop, escalate
4. **Scope creep**: Implementation starts requiring economic modeling, AI learning, multi-resource optimization, or anything beyond the 3 components listed above → stop, escalate

---

## Agent Assignment

**Assigned To**: Qwen (local worker)

**Why This Agent**: Rake/service implementation specialist; understands existing codebase patterns (e.g., `venus_mars:pipeline_v2` time-loop, `lunar_precursor_mission_validation.rake` rake structure); comfortable delegating to existing services rather than reimplementing.

**Supervision Level**: Standard — implement, test in Docker, report findings. Escalate gaps per Stop Conditions above; do NOT guess/invent data.

**Local Attempts Before Cloud**: All of this is local-executable (no cloud APIs needed). Do all work locally in Docker.

---

## Prerequisites — READ FIRST

1. **Read this entire task file** — especially the three-tier consumption model, Venus Mars reference pattern, and stop conditions
2. **Read TASK_TEMPLATE.md** (agent-tasks project) — ensure any follow-up work follows the same template format
3. **Review existing time-loop pattern**: `lib/tasks/venus_mars_pipeline.rake` (reference only; read-only)
4. **Review existing rakefile structure**: `lib/tasks/luna_mission.rake` (integration harness; confirm you do NOT modify this)
5. **Check existing services**:
   - `app/services/ai_manager/precursor_capability_service.rb` (line 20+, the `can_produce_locally?` method)
   - `app/services/logistics/transport_cost_service.rb` (import cost data; may be incomplete for MVP routes)
   - `app/services/logistics/shortage_detector.rb` (optional reference for detecting survival shortages)
6. **Review GameConstants**: `config/initializers/game_constants.rb` (HUMAN_LIFE_SUPPORT rates)
7. **Review Blueprint JSON files**: `data/json-data/missions_v2/blueprints/` (resource requirements, production rates)
8. **Review Operational Data JSON**: `data/json-data/missions_v2/operational_data/` (e.g., `solar_panel_array.json` maintenance_requirements)
9. **Check Agent Workflow README**: `docs/new_agent/README.md` (session conventions, completion report format)

---

## Acceptance Criteria

- [ ] New standalone rake exists at `lib/tasks/luna_operations_simulation.rake`, separate from `luna_mission.rake`
- [ ] Rake task `luna:simulate_operations` runs N daily ticks (default 30) against an already-deployed Luna settlement
- [ ] Each tick calculates production (ISRU via PrecursorCapabilityService), consumption (three tiers: life support + production + maintenance), and delta
- [ ] Inventory deltas persisted to settlement.inventory per tick
- [ ] Local-vs-import decision evaluated per tick per tracked resource using PrecursorCapabilityService.can_produce_locally?
- [ ] Decisions logged in format: `[Decision: IMPORT_X]` or `[Decision: LOCAL_ONLY]`
- [ ] No modifications to `luna_mission.rake` or `venus_mars:pipeline_v2`
- [ ] Scope confined to the 3 listed components — no economic engine, no learning, no multi-resource optimization
- [ ] All tests pass: new rake execution and tick service unit tests
- [ ] Docker execution confirmed: runs successfully, no environment-specific failures

---

## Dependencies

**Blocked by**: none (consumes already-deployed state; no new deployment work required)

**Blocks**:
- Sulfur deposit economics observation (depends on multi-cycle inventory tracking)
- Market-tier emergence work (depends on this baseline simulation)

**Related tasks** (reference only; do NOT modify):
- `2026-07-15-HIGH-FEATURE-FULL-PRECURSOR-MISSION-VALIDATION.md` (deferred; separate concern — interplanetary supply-chain validation, not base-operation time simulation)
- `2026-07-19-HIGH-BUGFIX-PROCUREMENT-CAN-PRODUCE-LOCALLY` (completed; this task's local-availability check depends on that fix)

---

## Completion Report Template

*Filled in by implementing agent upon completion*

**Completed by**: 
**Completion date**: 

### What was built
- New rake location, namespace, configurable day count
- Tick service class: input/output signature, per-tick execution flow
- Settlement model schema changes (if any)
- Decision logging format used
- Service integrations (PrecursorCapabilityService, TransportCostService, etc.)

### Issues discovered
- Any gaps in PrecursorCapabilityService coverage?
- Any missing import cost/transit data?
- Any unexpected schema conflicts?
- Any scope creep attempts and how they were handled?

### Follow-up tasks needed
- Economic modeling (post-MVP)
- Market pricing refinement
- Multi-resource optimization
- AI learning/adaptation layer

### Lessons learned
- What assumptions held up? What changed?
- Any reusable patterns from this spike for future work?
