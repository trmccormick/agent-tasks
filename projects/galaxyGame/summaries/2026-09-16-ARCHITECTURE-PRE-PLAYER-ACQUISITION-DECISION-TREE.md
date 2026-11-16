## STATUS SYNTHESIS REPORT

**Task**: 2026-09-12-MEDIUM-ARCHITECTURE-PRE-PLAYER-ACQUISITION-DECISION-TREE
**Status**: backlog → active
**Date**: 2026-09-16

### What I'm About to Do
Produce the authoritative pre-player acquisition decision tree, choose ResourceAcquisitionService as the single execution owner (with EscalationService as shortage/emergency spine), define integration points on `evaluate_strategy`, and document non-goals (no player-first, no parallel service, no full live-loop rewrite). All deliverables go into this summaries/ file — no code changes.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `galaxy_game/app/services/market/npc_price_calculator.rb` | evaluate_strategy (read-only) | ✅ confirmed at line 67 (class) + 403 (instance) |
| `galaxy_game/app/services/ai_manager/escalation_service.rb` | Spine — shortage/emergency decisions | ✅ confirmed exists, 627 lines |
| `galaxy_game/app/services/ai_manager/resource_acquisition_service.rb` | Path B execution owner | ✅ confirmed exists, evaluate_strategy call at line 140 |
| `galaxy_game/app/services/ai_manager/procurement_service.rb` | Path A — ISRU checks (placeholder market) | ✅ confirmed exists, 112 lines |
| `galaxy_game/app/services/ai_manager/operational_manager.rb` | Path A entry point | ✅ confirmed exists, calls ProcurementService at line 861 |
| `galaxy_game/app/services/ai_manager/resource_planner.rb` | Path B entry point | ✅ confirmed exists, calls ResourceAcquisitionService at line 140 |
| `galaxy_game/app/services/ai_manager/resource_fulfillment_service.rb` | Thin wrapper (no independent logic) | ✅ confirmed exists, 33 lines |
| `summaries/2026-09-07 inventory synthesis` | Dual-path baseline context | ✅ read and analyzed |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated backlog → active
- ✅ Read this task, inventory (2026-09-07), and wiring completion context
- ✅ Understand pre-player vs post-player split
- ✅ Confirmed all 7 reference file paths exist at `galaxy_game/app/services/...`
- ✅ Confirmed `evaluate_strategy` is live (class method line 67, instance method line 403)
- ✅ Confirmed `calculate_eap_ceiling` fully removed (zero matches in codebase)

### Expected Outcomes
1. Written pre-player decision tree (stockpile → local → cycler → emergency → import via evaluate_strategy)
2. Named single acquisition owner: ResourceAcquisitionService executes; EscalationService owns shortage/emergency decisions
3. `evaluate_strategy` as sole pricing source — documented integration contract
4. Ranked route option shape defined (cost, time, source type)
5. Explicit non-goals and follow-on (post-player tree, Material Sourcing revise)
6. No parallel service; no player-first implementation

### Critical Gotchas I Will Avoid
- ❌ Player buy orders / missions as default — instead ✅ pre-player tree only
- ❌ New acquisition service — instead ✅ extend existing owner (ResourceAcquisitionService)
- ❌ Hard-coded EAP multipliers or dead ceiling helpers — instead ✅ evaluate_strategy only

---

## PRE-PLAYER ACQUISITION DECISION TREE

### Decision Tree (Authoritative for Training-Curriculum Phase)

When a settlement needs material X and players are not yet in the economy:

```
START: Settlement needs material X
  │
  ├─ 1. Inventory + intentional stockpile sufficient?
  │     → YES: done (no action needed)
  │     → NO: continue to step 2
  │
  ├─ 2. Local production / harvest capability exists?
  │     (facility deployed OR ISRU processing unit active OR harvester + body resource available)
  │     → YES: produce/harvest locally (use existing capability, no new build-out)
  │     → NO: continue to step 3
  │
  ├─ 3. Normal shortage? (time-to-critical > time-to-next-resupply)
  │     → YES: wait for scheduled cycler / resupply (prefer patience over expensive action)
  │     → NO (emergency): continue to step 4
  │
  ├─ 4. Emergency — time-to-critical < time-to-next-resupply?
  │     Can local production stand up in time?
  │     → YES: force local production if it can stand up before critical threshold
  │     → NO (cannot stand up in time): continue to step 5
  │
  └─ 5. Last resort — import via evaluate_strategy
        Market::NpcPriceCalculator.evaluate_strategy(material:, location:, context:)
        Use reference_cost / strategy_type from result
        → Execute import order through ResourceAcquisitionService
```

### Cross-Cutting Rules

1. **Unified affordability**: Where a money model already exists, use it. Do not invent a second currency/affordability system.
2. **DC continuity**: Keep the settlement running even at temporary trade imbalance. Short-term deficit is acceptable if long-term trajectory is positive.
3. **Material data stays facility-based + baseline economics only**: No location-keyed sourcing blocks.
4. **Pricing truth = `evaluate_strategy` only**: No hard-coded EAP multipliers in acquisition code; no resurrected `calculate_eap_ceiling`.

### Post-Player Tree (Document Only — Do Not Implement)

After AWS network links Sol–Eden–(procedural) System B and players enter the economy:

```
stockpile → proactive buy orders → player-offered missions → local production → cycler wait → emergency → import
```

This is **follow-on work**, not this task. Documented here for architectural completeness only.

---

## SINGLE ACQUISITION OWNER DECISION

### Decision: ResourceAcquisitionService as Execution Owner; EscalationService as Spine

**Boundary definition:**

- **EscalationService** owns the *decision* layer: shortage detection, emergency vs normal classification, time-to-critical calculations, and strategy routing (automated_harvesting / deploy_manufacturing_unit / scheduled_import). It calls ResourceAcquisitionService for execution.
- **ResourceAcquisitionService** owns the *execution* layer: local-GCC vs external-USD fork, contract creation, import order fulfillment, and `evaluate_strategy` integration. It receives direction from EscalationService but makes its own affordability checks.

**Why this split:**
- ResourceAcquisitionService already has real pricing (via `evaluate_strategy` at line 140) and contract/order creation logic. Path B is the only path with working market integration.
- ProcurementService (Path A) has placeholder/stub market calls — it cannot be the canonical owner because its market path does not work.
- EscalationService already owns shortage/emergency classification and strategy routing — making it the decision spine avoids duplicating that logic.

**What this rejects:**
- ❌ ProcurementService as canonical execution owner (market path is stub)
- ❌ New ProcurementOrchestrator / AcquisitionService (parallel architecture)
- ❌ Dual ambiguous ownership (both paths equally valid — they are not)

---

## INTEGRATION CONTRACT

### Input
- `settlement`: Settlement instance needing material X
- `material_id`: Material identifier (e.g., `:graphite`, `:epoxy_resin`)
- `urgency`: `:normal` | `:emergency` (optional, defaults to `:normal`)
- `context`: Hash with optional keys (`cycler_eta`, `time_to_critical`, `body_name`)

### Call
```ruby
Market::NpcPriceCalculator.evaluate_strategy(
  material: material_id,
  location: settlement.location,
  context: { urgency: urgency, body: body_name }
)
```

### Output Shape (Ranked Route Options)
Documented shape — not fully coded in this task:

```ruby
{
  source_type: :local | :cycler_wait | :emergency_local | :import,
  cost: <numeric>,              # from evaluate_strategy.reference_cost or local production estimate
  time_to_fulfill_hours: <numeric>,
  strategy_type: <string>,       # from evaluate_strategy.strategy_type (e.g., "consumable", "hardware_capex")
  affordability: {               # optional, where unified money model exists
    can_afford: <boolean>,
    remaining_after: <numeric>
  }
}
```

### Where the Tree Lives
The decision tree should live as a method on **EscalationService** (e.g., `determine_acquisition_route(settlement, material_id, urgency:)`) because:
1. EscalationService already owns shortage/emergency classification
2. It can call ResourceAcquisitionService for execution after deciding the route
3. It keeps the decision spine and execution owner clearly separated

---

## NON-GOALS

The following are explicitly **out of scope** for this task and any follow-on implementation:

- ❌ Player buy orders (post-AWS only)
- ❌ Player-offered missions as first escalation (post-AWS only)
- ❌ Full Material JSON migration / audit
- ❌ Multi-system network coordinator
- ❌ Foothold ranking or cross-settlement optimization
- ❌ Live tick wiring of every branch in this task
- ❌ New procurement/acquisition service
- ❌ Hard-coded EAP multipliers
- ❌ Resurrecting `calculate_eap_ceiling`
- ❌ Location-keyed material sourcing blocks

---

## FOLLOW-ON TASKS

1. **Post-player acquisition tree** — implement buy orders + player missions path (separate task, after AWS network)
2. **Revise or supersede** `2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md` — stale relative to pre-player rules and `evaluate_strategy`
3. **Implementation task**: Wire the pre-player decision tree onto EscalationService as a method, delegating execution to ResourceAcquisitionService
4. **Optional**: Deeper `evaluate_strategy` adoption in EscalationService (currently uses old `calculate_bid`/`calculate_ask`)

---

## FILES INVOLVED — CONFIRMED EXISTENCE

| File | Status |
|---|---|
| `galaxy_game/app/services/market/npc_price_calculator.rb` | ✅ exists, evaluate_strategy at line 67 + 403 |
| `galaxy_game/app/services/ai_manager/escalation_service.rb` | ✅ exists, 627 lines |
| `galaxy_game/app/services/ai_manager/resource_acquisition_service.rb` | ✅ exists, evaluate_strategy call at line 140 |
| `galaxy_game/app/services/ai_manager/resource_fulfillment_service.rb` | ✅ exists, 33 lines |
| `galaxy_game/app/services/ai_manager/procurement_service.rb` | ✅ exists, 112 lines |
| `galaxy_game/app/services/ai_manager/operational_manager.rb` | ✅ exists, ProcurementService call at line 861 |
| `galaxy_game/app/services/ai_manager/resource_planner.rb` | ✅ exists, ResourceAcquisitionService call at line 140 |

---

## MATERIAL SOURCING 2026-09-03 STATUS

**Task**: `2026-09-03-MEDIUM-ARCHITECTURE-MATERIAL-SOURCING-AND-ACQUISITION-LOGIC.md`
**Status**: STALE — do not dispatch as-is
**Reason**: Decision tree and integration assumptions are stale relative to pre-player rules and `evaluate_strategy`. This task supersedes its acquisition architecture decisions. Material Sourcing may be revised or superseded after this task lands.

---

**SYNTHESIS COMPLETE.** Ready for human review and approval before implementation task creation.
