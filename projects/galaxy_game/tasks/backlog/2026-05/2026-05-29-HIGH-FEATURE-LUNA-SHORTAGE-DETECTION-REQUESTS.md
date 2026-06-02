---
status: backlog
priority: HIGH
type: feature
system_domain: AI_MANAGER
mvp_alignment: LUNA_SETTLEMENT_AUTONOMOUS_TESTING
local_worker_safe: true
---

# TASK: Luna Phase 3 — Shortage Detection & Import Request Generation

**Status**: BACKLOG  
**Priority**: HIGH  
**Type**: feature  
**Created**: 2026-05-29  
**Last Updated**: 2026-05-29  

---

## Agent Assignment

**Assigned To**: GPT-5 mini (0x)  
**Why This Agent**: Integrates with AI Manager, requires orchestration logic, and depends on Phase 1 & 2.  
**Supervision Level**: standard

---

## Context

Luna settlement operates autonomously. When inventory drops below thresholds, it should automatically detect shortages and generate import requests. This completes the supply chain loop:

Shortage Detected → Import Request Created → Market Route → Phase 1 (Cost) & Phase 2 (Manifest) Used → Trade Executed

**Dependencies**: 
- Derived from `2026-05-29-ANALYSIS-LUNA-BASE-BUILDUP-SUPPLY-CHAIN.md` (Q9 finding: "No automated shortage detection and import request generation is present")
- Depends on Phase 1 (Cost Analysis) and Phase 2 (Manifest Generation)

---

## Problem Statement

**Gap**: Settlements don't detect when inventory is critically low and need to import.

**Current state**: Shortages must be manually detected or AI Manager doesn't respond to them.

**Missing pieces**:
1. ShortageDetector: Monitor inventory vs thresholds
2. ImportRequestGenerator: Create procurement orders when shortage detected
3. Integration with AI Manager: Trigger detection after production, consumption, or on schedule

---

## Specification

### Service 1: `Logistics::ShortageDetector`

**File**: `galaxy_game/app/services/logistics/shortage_detector.rb`

#### Method: `self.detect_shortages(settlement, threshold_percent = 20)`

**Input**:
- `settlement` (Settlement::BaseSettlement)
- `threshold_percent` (Integer): How low inventory must be to trigger (default 20%)

**Output**: Array of Hash with structure:
```ruby
[
  {
    resource: "Steel",
    current_inventory: 50,
    target_inventory: 500,      # based on production requirements or config
    shortage_amount: 450,
    shortage_percent: 90,
    critical: true              # if below 10% of target
  },
  ...
]
```

**Logic**:
- For each resource in settlement.inventory:
  - Get current quantity
  - Determine target quantity (from settlement operational_data or game constants)
  - Calculate shortage if current < target * threshold_percent
  - Mark as critical if very low
  - Return all shortages found

#### Method: `self.calculate_target_inventory(settlement, resource)`

**Purpose**: Determine what "healthy" inventory level is
**Logic**: 
- Check settlement.operational_data for resource-specific targets
- Fall back to consumption_rates if defined
- Fall back to game constants if nothing else
- Return sensible default (e.g., 30 days of consumption)

---

### Service 2: `Logistics::ImportRequestGenerator`

**File**: `galaxy_game/app/services/logistics/import_request_generator.rb`

#### Method: `self.generate_import_request(settlement, shortage_data)`

**Input**:
- `settlement` (Settlement::BaseSettlement)
- `shortage_data` (Hash): Single shortage entry from ShortageDetector output

**Output**: ImportRequest object (see below)

**Logic**:
- Create ImportRequest record with shortage details
- Use Phase 1 (Cost Analysis) to determine import vs produce decision
- Use Phase 2 (Manifest) to prepare cargo list
- Create corresponding Market::Order (sell order) if appropriate
- Trigger Trade Execution Service
- Return request object

#### Model: `Logistics::ImportRequest`

**Attributes**:
```ruby
- id (Integer)
- settlement_id (Integer)
- resource (String)
- quantity_needed (Integer)
- cost_analysis (JSON)      # data from Phase 1 (CostAnalyzer)
- manifest_id (String)      # from Phase 2 if generated
- status (Enum): :created, :quoted, :ordered, :fulfilled, :cancelled
- created_at (DateTime)
- resolved_at (DateTime, nullable)
```

---

### Integration: AI Manager Service Coordinator

**File to Modify**: `galaxy_game/app/services/ai_manager/service_coordinator.rb`

**Add Method**: `detect_and_request_imports(settlement)`

**When Called**:
- After each production cycle (from AIManager::Manager)
- On scheduled check (e.g., every N game ticks)
- Triggered by explicit shortage alert

**Logic**:
```ruby
shortages = ShortageDetector.detect_shortages(settlement)
shortages.each do |shortage|
  if shortage[:critical]
    ImportRequestGenerator.generate_import_request(settlement, shortage)
  end
end
```

---

## Files to Modify/Create

**Create**:
- `galaxy_game/app/models/logistics/import_request.rb` — ImportRequest model
- `galaxy_game/app/services/logistics/shortage_detector.rb` — Detector service
- `galaxy_game/app/services/logistics/import_request_generator.rb` — Generator service
- `galaxy_game/spec/services/logistics/shortage_detector_spec.rb` — Tests
- `galaxy_game/spec/services/logistics/import_request_generator_spec.rb` — Tests
- Migration: `db/migrate/[timestamp]_create_logistics_import_requests.rb`

**Modify**:
- `galaxy_game/app/services/ai_manager/service_coordinator.rb` — Add import detection call
- `galaxy_game/app/models/settlement/base_settlement.rb` — Add `has_many :import_requests` association

---

## Test Cases

### ShortageDetector Tests

1. **No shortage**: Inventory above threshold
   - Assert empty array returned

2. **Single shortage**: One resource below threshold
   - Assert shortage detected
   - Assert critical flag correct based on severity

3. **Multiple shortages**: Several resources low
   - Assert all detected
   - Assert quantities correct

4. **Custom threshold**: Detect at different threshold percentages
   - Assert threshold parameter respected

5. **Empty inventory**: Settlement has no inventory items
   - Assert handles gracefully (no crashes)

### ImportRequestGenerator Tests

1. **Create request**: Generate import for shortage
   - Assert ImportRequest created
   - Assert status is :created
   - Assert settlement_id linked

2. **Use Cost Analysis**: Request includes cost comparison
   - Assert cost_analysis populated
   - Assert recommendation (import vs produce) included

3. **Generate Manifest**: Request creates manifest if needed
   - Assert manifest_id populated
   - Assert manifest grouped items

4. **Create Market Order**: Request triggers trade if importing
   - Assert Market::Order created
   - Assert Order routed to LDC or market

---

## Integration Points

**Depends on**:
- Phase 1 (Cost Analysis Service)
- Phase 2 (Manifest Generation Service)
- AI Manager ServiceCoordinator
- Settlement model
- Market Order system

**Used by**:
- AI Manager autonomous settlement logic
- Scheduled job/tick system (future)

---

## Success Criteria

✅ ShortageDetector working  
✅ ImportRequestGenerator working  
✅ Both services integrated with AI Manager  
✅ All test cases passing  
✅ Cost Analysis used correctly in decisions  
✅ Manifests generated for import requests  
✅ Market Orders created when importing  
✅ Graceful handling of edge cases  

---

## Notes

- Integration with TradeExecutionService happens at Market Order creation
- ShortageDetector can run frequently (low cost, read-only)
- ImportRequestGenerator creates records and triggers trades (run carefully)
- Monitor performance if settlement has thousands of inventory items
- Future: tie to game tick/time system for realistic supply chain pacing
