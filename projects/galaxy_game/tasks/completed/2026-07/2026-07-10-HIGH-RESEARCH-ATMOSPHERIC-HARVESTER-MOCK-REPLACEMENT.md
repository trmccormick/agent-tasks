---
status: completed
priority: HIGH
type: research
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
completed_date: 2026-07-11
---

## Research Complete

**Synthesis report**: `docs/new_agent/projects/galaxy_game/summaries/2026-07-10-RESEARCH-ATMOSPHERIC-HARVESTER-MOCK-REPLACEMENT.md`

### Key Findings
- **No new Corporation entity needed** — any `Corporation` can own skimmers via existing polymorphic `BaseCraft#owner`
- **`TerraSim::AtmosphericTransferService` already implements correct physics** — raw transfer mode with proportional extraction, Titan 5% limit, 98% transport efficiency
- **MVP defers market integration** — gases flow to cycler → Luna settlement atmosphere; market listing is Phase 9+
- **No blockers** — ownership model exists, docking infrastructure exists, NPC pricing exists

### Design Decision
Refactor `AIManager::AtmosphericHarvesterService` → new `AIManager::AtmosphericExtractionService` that delegates to `TerraSim::AtmosphericTransferService` with ownership validation. Skimmers use `:raw` transfer mode only (proportional extraction).

### Next Steps
1. Create `AIManager::AtmosphericExtractionService`
2. Add skimmer → cycler cargo transfer method
3. Update AI Manager decision logic to replace mock calls
4. Write specs
**Type**: research
**Created**: 2026-07-10
**Last Updated**: 2026-07-10 — Research Agent (Qwen3.6-35b)

---

## Dependency

**Primary**: `docs/new_agent/projects/galaxy_game/summaries/2026-07-09-RESEARCH-ATMOSPHERIC-SKIMMER-AUDIT.md`
- Section "AIManager::AtmosphericHarvesterService — Returns MOCK data"
- Current code returns hardcoded values: `collect_nitrogen(titan) = 200`, `collect_methane(titan) = 150`

---

## Problem Statement

The current `AIManager::AtmosphericHarvesterService` is a **placeholder with mock data** that does NOT modify actual atmosphere state:

```ruby
# CURRENT (WRONG) — hardcoded mock values, no real extraction
def collect_nitrogen(titan)
  titan[:nitrogen_collected] = 200  # Hardcoded mock value
end

def collect_methane(titan)
  titan[:methane_collected] = 150   # Hardcoded mock value
end
```

**This is fundamentally wrong for two reasons:**

1. **Mock data**: Returns hardcoded values instead of real atmospheric extraction
2. **Wrong semantics**: Implies selective gas collection at the source, which contradicts skimmer intent (skimmers extract raw mixed gas, not targeted individual gases)

### Correct Behavior Per Skimmer Intent

Skimmers extract **raw mixed atmospheric gas** proportionally by percentage — they cannot target specific gases in the atmosphere. The separation happens later at the LDC Gas Separator on Luna.

---

## Research Requirements

### 1. Replacement Strategy

Research and design how to replace the mock service:
- Should `AIManager::AtmosphericHarvesterService` be removed entirely?
- Should it be refactored to delegate to `AtmosphericTransferService`?
- What is the correct API surface for the AI Manager to request atmospheric extraction?

### 2. AstroLift Asset Authentication

Design authentication/authorization checks:
- Skimmers are **AstroLift-owned assets**, not LDC infrastructure
- How does the AI Manager verify AstroLift owns the skimmer requesting extraction?
- What credentials/tokens are needed for cross-corporation requests (AstroLift → LDC)?

### 3. Request Routing to AtmosphericTransferService

Design how extraction requests should be routed:
```ruby
# Proposed new service structure
class AIManager::AtmosphericExtractionService
  def initialize(skimmer, source_body)
    @skimmer = skimmer
    @source_body = source_body
  end

  def execute(transfer_params)
    # 1. Verify AstroLift owns @skimmer
    # 2. Validate transfer params (capacity, mode, etc.)
    # 3. Delegate to AtmosphericTransferService
    TerraSim::AtmosphericTransferService.new(@source_body, target_body).transfer_atmosphere(transfer_params)
  end
end
```

Research the correct routing pattern:
- Should the AI Manager create a task/job that triggers extraction?
- Should it be synchronous (direct call to transfer service) or asynchronous (queued job)?
- How does the AI Manager track extraction results for market pricing?

### 4. Market Integration

Design how extracted gases flow to the market:
- AstroLift's AI Manager sells processed/raw gases on the market
- LDC buys raw gas from AstroLift, processes it, resells on market
- How are prices determined for inter-corporation transactions?
- What market data does the AI Manager need to track?

### 5. MVP Scope vs Phase 9+

Determine what is MVP-relevant vs terraforming:
- **MVP**: Replace mock service with real `AtmosphericTransferService` delegation; validate extraction tracking for fuel delivery
- **Phase 9+**: Full market integration, inter-corporation pricing, multi-world atmospheric interaction

---

## Deliverables

1. Design document specifying replacement service structure
2. AstroLift asset authentication requirements
3. Request routing design (synchronous vs asynchronous)
4. Market integration requirements for MVP
5. MVP vs Phase 9+ scope boundary definition

---

## Stop Conditions — escalate to user immediately if:
- No clear ownership model exists for skimmers in the codebase
- Any architectural decision is required before research can continue
