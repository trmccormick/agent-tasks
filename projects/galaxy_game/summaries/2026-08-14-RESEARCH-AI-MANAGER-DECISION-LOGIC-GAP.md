# AI Manager Decision-Logic Gap Analysis

**Date**: 2026-08-14
**Type**: RESEARCH
**Task**: 2026-08-08-MEDIUM-RESEARCH-AI-MANAGER-DECISION-LOGIC-GAP.md
**Confidence**: High (thorough codebase search performed)

---

## Executive Summary

The AI Manager decision-logic layer — "given a location + intent, evaluate settlement options" — is **confirmed as not implemented**. The codebase contains supporting infrastructure (logistics contracts, import request generation, strategy selection) but none of the core decision-logic systems described in the LUNA MVP simulation design.

---

## Q1: ImportRequestGenerator — Multiple Supply Sources or Earth-Only?

### Answer: **Earth-only** (Confidence: High)

### Evidence

`Logistics::ImportRequestGenerator` exists and is wired into the AI Manager pipeline, but it has **no concept of alternative suppliers**. The class generates import requests for materials not available locally by sourcing them from Earth. There is no `sources` array, no supplier-priority mechanism, and no Venus/Titan/local-production fallback logic.

**Systems that support this finding:**
- `Logistics::ImportRequestGenerator` — generates import requests with Earth as the sole source
- AI Manager pipeline integration confirms it's called during settlement evaluation

### Gap Assessment
This is a **known gap** for Phase 7+ implementation. The design doc anticipates multi-source supply chains (Venus skimmer, Titan delivery, local production) but the current codebase only models Earth imports.

---

## Q2: Inbound Cargo/Manifest Tracking

### Answer: **Does NOT exist** (Confidence: High)

### Evidence

No model or service exists for tracking "craft X is inbound with payload Y, arriving in Z days." Searched for:
- `InboundManifest`, `CargoManifest`, `InboundCraft`, `TransitTracking`, `ArrivalSchedule` — none found
- `Logistics::Contract` — no arrival tracking fields
- `AIManager::TaskExecutionEngineV2` — tracks mission phases but not ETA-based cargo transit
- Any "inbound", "transit", or "arrival" models/services — none found

### Gap Assessment
This is a **confirmed gap**. The simulation baseline requires inbound cargo tracking for interplanetary logistics but no such system exists in the codebase.

---

## Q3: Precursor Build Sequencing as Dependency Graph

### Answer: **DAG exists in JSON but NOT wired into runtime** (Confidence: High)

### Evidence

- `luna_precursor_mission_plan_v2.json` contains a `dag_execution_order` array
- No code references `dag_execution_order`, `dependency_graph`, `build_sequence`, or `precursor_scheduler` in app/lib
- `AIManager::TaskExecutionEngineV2` executes flat lists — no phase dependency resolution
- `AIManager::PrecursorBuildScheduler` does not exist

### Gap Assessment
The DAG is **data-only** (exists in the mission plan JSON) but has **no runtime implementation**. Build sequencing is currently a flat list, not a dependency graph. This is a confirmed gap for Phase 7+.

---

## Q4: CH4/Scarce-Resource Priority-Queue Arbitration

### Answer: **Does NOT exist** (Confidence: High)

### Evidence

Searched for:
- `CH4Allocation`, `ResourceArbitration`, `PriorityQueue`, `ScarceResource` — none found
- `StrategySelector` — no allocation logic present
- `AIManager::MarketStabilizationService` — handles market stabilization, not resource allocation between competing demands

### Gap Assessment
This is a **confirmed gap**. No scarce-resource arbitration mechanism exists. The AI Manager has no logic for allocating CH4 or other scarce resources between competing demands (e.g., fuel vs. feedstock).

---

## Q5: Skimmers as Persistent Assets with Fuel State, Location, Mission Phase

### Answer: **Does NOT exist** (Confidence: High)

### Evidence

Searched for:
- `Skimmer`, `HarvesterCraft`, `AtmosphericHarvester` models/services — none found
- `create_table.*skimmer` in db/migrate — no skimmers table exists
- `HeavyLiftTransport` — exists but is a transport unit, not an atmospheric harvester
- No craft type has fuel state, location tracking, or mission phase fields for atmospheric operations

### Gap Assessment
This is a **confirmed gap**. Skimmers (atmospheric harvesters) are not modeled as persistent assets. The design doc anticipates skimmer fleets with fuel state, location, and mission phase — none of this exists in the codebase.

---

## Summary: Systems That EXIST vs DO NOT EXIST

### Systems That EXIST
| System | Location | Notes |
|--------|----------|-------|
| `Logistics::ImportRequestGenerator` | app/lib/logistics/import_request_generator.rb | Earth-only import generation |
| `AIManager::TaskExecutionEngineV2` | app/lib/ai_manager/task_execution_engine_v2.rb | Flat list execution, no DAG |
| `AIManager::StrategySelector` | app/lib/ai_manager/strategy_selector.rb | No allocation logic |
| `AIManager::MarketStabilizationService` | app/lib/ai_manager/market_stabilization_service.rb | Market stabilization only |
| `Logistics::Contract` | app/lib/logistics/contract.rb | No arrival tracking |
| `HeavyLiftTransport` | app/lib/heavy_lift_transport.rb | Transport unit, not skimmer |

### Systems That DO NOT EXIST (Confirmed Gaps)
| System | Impact |
|--------|--------|
| Multi-source supply chain (Venus/Titan/local) | Q1 gap — import requests Earth-only |
| Inbound cargo/manifest tracking | Q2 gap — no interplanetary logistics tracking |
| Precursor build DAG scheduler | Q3 gap — JSON data exists but no runtime wiring |
| CH4/scarce-resource arbitration | Q4 gap — no resource allocation logic |
| Skimmer persistent asset model | Q5 gap — atmospheric harvesters not modeled |

---

## Recommendations for Next Steps

1. **Phase 7+ Implementation Priority** (in order):
   - **P0**: Precursor build DAG scheduler — most foundational, blocks all other sequencing
   - **P1**: Multi-source supply chain — enables Venus/Titan/local production integration
   - **P2**: Inbound cargo/manifest tracking — required for interplanetary logistics realism
   - **P3**: Skimmer persistent asset model — enables atmospheric harvesting gameplay
   - **P4**: CH4/scarce-resource arbitration — complex but high gameplay value

2. **Design Phase**: Before implementation, the Outstanding Architecture Questions in `LUNA-MVP-SIMULATION-DESIGN.md` should be marked as resolved with these findings. The design doc should be updated to reflect confirmed gaps vs. existing infrastructure.

3. **Risk Assessment**: All 5 gaps are in the decision-logic layer, which is a cohesive architectural boundary. Implementation can proceed module-by-module without cross-cutting dependencies (except DAG scheduler → build sequencing dependency).

---

## LUNA-MVP-SIMULATION-DESIGN.md Update Required
- [ ] Mark Outstanding Architecture Questions section as resolved
- [ ] Add findings summary to each question
- [ ] Note that all 5 questions confirmed gaps (no hidden implementations found)
