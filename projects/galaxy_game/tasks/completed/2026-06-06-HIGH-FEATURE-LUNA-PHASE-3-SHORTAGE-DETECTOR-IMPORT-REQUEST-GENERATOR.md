# Luna Phase 3 — ShortageDetector + ImportRequestGenerator → AI Manager

```yaml
id: 2026-06-06-HIGH-FEATURE-LUNA-PHASE-3-SHORTAGE-DETECTOR-IMPORT-REQUEST-GENERATOR
status: backlog
priority: HIGH
type: FEATURE
created: 2026-06-06
agent: Qwen3.5-27B
phase: Luna Phase 3
gate: Luna Phase 1 ✅ Phase 2 ✅
```

---

## Context & Strategic Purpose

Luna is the industrial foundation for all Sol system expansion. Without a
self-sustaining Luna, nothing downstream is viable:

- L1 shipyard requires Luna-sourced materials
- LEO depot requires Luna-sourced LOX
- AstroLift cost reduction (Phase 2 pricing era) requires LEO depot
- Mars/Venus/Titan expansion requires affordable logistics

Phase 3 closes the shortage response loop for the Luna NPC settlement.
The AI Manager can already detect cost pressure (Phase 1) and create manifests
(Phase 2). Phase 3 wires real shortage quantities from ShortageDetector into
the manifest creation flow, replacing the Phase 2 stub of 10 units per resource.

This is Act 1 world building — pure NPC operation. No player mission offers,
no player contracts, no PlayerContractService hooks. LDC manages Luna. AstroLift
moves cargo. GCC circulates NPC-to-NPC via Virtual Ledger.

---

## Supply Chain Context (Luna MVP)

Luna has three inbound supply sources:

1. **Earth → Luna via AstroLift** — structural materials, equipment, manufactured
   goods. EARTH_LUNA_TRANSIT_DAYS = 3. Expensive (150 GCC/kg Earth Anchor Price
   era). Emergency backstop, not primary strategy. Import dependency should
   decline as ISRU matures.

2. **Luna ISRU (local)** — regolith-derived oxygen, metals, structural materials.
   Growing over time. Goal is to make Earth imports unnecessary for most resources.

3. **Venus/Titan skimmers → Luna** — exotic gases, hydrocarbons, nitrogen.
   Separate supply chain. NOT handled by ImportRequestGenerator. Do not wire
   gas shortage resolution through the import request flow — skimmer returns
   are scheduled separately and AstroLift cannot substitute for them.

Phase 3 handles supply chain #1 only — Earth imports for survival shortages
that ISRU cannot yet cover. Gas shortages (supply chain #3) are out of scope.

---

## Economic Context

LDC is the GCC anchor — its purchasing activity injects GCC into the NPC
economy. Every ImportRequest created by the AI Manager on behalf of LDC is
an economic event. AstroLift receives GCC payment, circulating currency through
the logistics layer. NPC-to-NPC settlement uses Virtual Ledger where GCC
reserves are insufficient.

Flag ImportRequest creation as an economic event in code comments for future
hookup to GCC ledger/mint tracking.

---

## Scope

### In Scope
- Wire `ShortageDetector.detect_shortages(settlement)` into `StrategySelector#execute_cost_reduction`
- Replace `default_quantity_for(resource)` stub (returns 10) with real quantities from shortage report
- Call `ImportRequestGenerator.generate_import_request` (singular) per survival shortage item
- Fix source/destination bug in `ImportRequestGenerator#generate_import_request` — currently
  passes `settlement, settlement` as both source and destination. Source must be
  Cape Canaveral Spaceport (Earth source), destination is the Luna settlement.
  Use existing `find_source_settlement` from Phase 2.
- Handle `ShortageDetector` returning `viable: false` gracefully — log and return false
- Handle empty `survival_shortages` gracefully — log and return false
- Add focused RSpec specs for all new behavior
- Run ai_manager suite after implementation — must be 0 failures

### Out of Scope
- Gas shortage resolution (skimmer supply chain — separate system)
- Resource type routing (which shortage maps to which supply chain — Phase 4+)
- Multi-source selection (Cape Canaveral is the only Earth source for now)
- Player mission offers (Act 2+)
- Ship availability / position tracking (future logistics routing)
- Moving `find_source_settlement` to Logistics layer (correct long-term home,
  not Phase 3 work)
- LEO depot routing (not yet built)
- Virtual Ledger integration (already handled by existing contract flow)

---

## Key Files

### Read Before Writing Any Code
- `app/services/ai_manager/strategy_selector.rb` — implementation target
- `spec/services/ai_manager/strategy_selector_spec.rb` — spec target
- `app/services/logistics/shortage_detector.rb` — public API reference
- `app/services/logistics/import_request_generator.rb` — public API reference
- `app/services/logistics/manifest_generator.rb` — confirm create_manifest signature

### Public APIs Confirmed

**ShortageDetector:**
```ruby
Logistics::ShortageDetector.detect_shortages(settlement)
# Returns:
# {
#   viable: true/false,
#   survival_shortages: [{ material:, need:, have:, priority: 'critical' }],
#   expansion_shortages: [{ material:, need:, have:, priority: 'expansion' }],
#   total_shortages: [...]
# }
# Returns { viable: false, survival_shortages: [], expansion_shortages: [] }
# if ISRUCapabilityManager.has_basic_isru?(settlement) is false
```

**ImportRequestGenerator (singular — confirmed spec API):**
```ruby
Logistics::ImportRequestGenerator.generate_import_request(settlement, shortage)
# shortage hash keys: :resource or :material, :amount or :need or :quantity_needed
# Calls CostAnalyzer.compare_costs internally
# Calls ManifestGenerator.create_manifest internally
# Raises ImportRequestError if manifest generation fails
# Returns persisted ImportRequest record
```

**WARNING — plural method is not the target:**
`generate_import_requests` (plural) exists in ImportRequestGenerator and is
called by ServiceCoordinator. Do not touch it. Do not replace it. The singular
`generate_import_request` is the correct API for this task.

**Source/destination bug to fix in generate_import_request:**
Current code passes `settlement, settlement` to `ManifestGenerator.create_manifest`.
This is wrong. Fix to pass `source_settlement, settlement` where source_settlement
is resolved via `find_source_settlement` (already implemented in StrategySelector
Phase 2). Confirm whether to fix inside ImportRequestGenerator or pass source
from StrategySelector — read both files and decide based on what's cleaner.

---

## Implementation Notes

### In StrategySelector#execute_cost_reduction

Current Phase 2 implementation:
- Calls `find_source_settlement` to get Cape Canaveral
- Calls `ManifestGenerator.create_manifest` directly with stub quantity of 10
- Returns true/false based on manifest result

Phase 3 changes:
- Call `ShortageDetector.detect_shortages(settlement)` first
- Return false with log if `viable: false`
- Return false with log if `survival_shortages` empty
- For each survival shortage, call `ImportRequestGenerator.generate_import_request`
- Pass shortage hash — keys are `:material` and `:need` from ShortageDetector output
- Rescue `ImportRequestGenerator::ImportRequestError` per shortage item — log warning,
  continue processing remaining shortages (don't abort full loop on single failure)
- Return true if at least one ImportRequest created successfully

### Quantity Logic
ShortageDetector returns `need` (target quantity) and `have` (current stock).
The import quantity should be `need - have` (the actual deficit), not `need`
in full. Read ShortageDetector output carefully — wire the deficit, not the target.

### Consumption Rate Note
ShortageDetector currently does simple `current < target` comparison without
factoring in consumption rate. This means import quantities may be understated
(settlement consumes during transit). Document this as a known limitation in
a TODO comment. Do not attempt to solve it in Phase 3.

### ISRU Maturity Signal
If Earth imports are being triggered frequently for resources Luna should be
producing locally, that is a signal ISRU expansion is needed — not that imports
should continue. Add a log warning when import requests are created for resources
that should be ISRU-producible (oxygen, metals). Tag as future AI Manager
decision point.

---

## Spec Requirements

Add focused specs to `strategy_selector_spec.rb` covering:

1. `execute_cost_reduction` calls `ShortageDetector.detect_shortages`
2. Returns false when `viable: false` from ShortageDetector
3. Returns false when `survival_shortages` is empty
4. Calls `generate_import_request` once per survival shortage item
5. Returns true when at least one ImportRequest created successfully
6. Continues processing remaining shortages when one raises `ImportRequestError`
7. Does not call `generate_import_request` for expansion shortages

Stub `ShortageDetector.detect_shortages`, `ImportRequestGenerator.generate_import_request`,
and `find_source_settlement` in specs. Do not hit the database for shortage detection.

---

## Acceptance Criteria

- [ ] `execute_cost_reduction` calls `ShortageDetector.detect_shortages`
- [ ] Graceful handling of `viable: false` — logs, returns false
- [ ] Graceful handling of empty `survival_shortages` — logs, returns false
- [ ] One `generate_import_request` call per survival shortage
- [ ] Expansion shortages ignored (Phase 4+ concern)
- [ ] Source/destination bug fixed — Cape Canaveral as source, Luna as destination
- [ ] `ImportRequestError` rescued per item — loop continues on single failure
- [ ] `default_quantity_for` stub removed or replaced
- [ ] All new behavior covered by specs
- [ ] ai_manager suite: 0 failures after implementation
- [ ] Full suite: still 3957 examples, max 3 failures (confirmed flaky only)

---

## Flaky Specs — Never Touch

| Spec | Line |
|---|---|
| `spec/services/construction/orbital_shipyard_service_spec.rb` | 129 |
| `spec/services/generators/game_data_generator_spec.rb` | 13 |
| `spec/services/lookup/material_lookup_service_spec.rb` | 251 |

Full suite baseline: **3957 examples, 3 failures, 57 pending**
ai_manager suite baseline: **731 examples, 0 failures, 4 pending**

---

## Future Seams (Document in Code, Don't Implement)

- **Skimmer supply chain** — gas shortages need a separate resolution path,
  not Earth imports via AstroLift
- **Source selection** — `find_source_settlement` belongs in Logistics layer
  eventually, not StrategySelector. For non-Luna worlds, source depends on
  proximity, provider availability, and ship position — not hardcoded Earth.
- **Ship availability/position** — current model assumes AstroLift can always
  fulfill. Real routing needs ship tracking.
- **Consumption-aware ordering** — import quantity should factor in transit time
  × consumption rate, not just current deficit.
- **ISRU maturity signal** — if imports are frequent for ISRU-producible resources,
  AI Manager should escalate ISRU expansion priority.
- **GCC ledger hookup** — ImportRequest creation is an economic event, tag for
  future mint/ledger tracking.
- **Portal transit** — future `Logistics::Contract` will need `transit_method: portal`
  with near-zero transit time once portal gates are established within Sol.

---

## Synthesis Report Required

Before applying any changes, produce a synthesis report covering:
1. Current `execute_cost_reduction` implementation — full method body
2. Confirm `ShortageDetector.detect_shortages` public API matches this task file
3. Confirm `generate_import_request` (singular) signature and shortage key names
4. Confirm `find_source_settlement` location and return value
5. Confirm `default_quantity_for` stub location — exact line to remove/replace
6. Identify source/destination bug exact location in ImportRequestGenerator
7. Flag any nil risks in the shortage → import request flow
8. Proposed spec structure before writing any specs

Stop after synthesis report. Do not apply changes until explicitly told to proceed.
