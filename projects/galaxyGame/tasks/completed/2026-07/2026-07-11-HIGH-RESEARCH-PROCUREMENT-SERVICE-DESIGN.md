---
status: backlog
priority: HIGH
type: research
system_domain: AI_MANAGER | MARKET
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: false
agent_routing: Gemini (design architecture questions)
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Research Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-07-11-HIGH-RESEARCH-PROCUREMENT-SERVICE-DESIGN.md

This is a pure research task for Gemini (web-based agent). You have NO codebase access.
All information you need is provided in the task file below. Answer the 4 research questions
using your own knowledge of the Galaxy Game architecture and any public documentation you can access.

CRITICAL: Save your findings as a markdown report to:
  /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/summaries/2026-07-11-RESEARCH-PROCUREMENT-SERVICE-DESIGN.md

Do NOT attempt to access any local files or run any commands.
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# RESEARCH: Clarify `can_produce_locally?` Design and Resource-to-Unit Mapping
**Status**: BACKLOG
**Priority**: HIGH
**Type**: research
**Created**: 2026-07-11

---

## The Problem

The `can_produce_locally?` method in `ProcurementService` is currently a stub that returns `true` for all inputs. A refactor was attempted but has design errors:

```ruby
# Current stub (lines 27-35 of procurement_service.rb)
def self.can_produce_locally?(settlement, resource, amount)
  case resource
  when :oxygen
    settlement_has_facility?(settlement, :atmospheric_processor)
  when :water
    settlement_has_facility?(settlement, :isru_processor)
  when :structural_carbon
    settlement_has_facility?(settlement, :cnt_fabricator)
  else
    false
  end
end
```

**Problems**:
1. Unit types don't exist: `atmospheric_processor` (Luna has no atmosphere), `isru_processor` (doesn't exist), `cnt_fabricator` (doesn't exist)
2. Only 3 resources hardcoded, but multiple units can produce the same resource (greenhouse + regolith extractor both produce oxygen)
3. Resource naming mixes display names (`:oxygen`, `:water`) with what appears to be intended chemical formula format (`O2`, `H2O`)
4. No clear logic for why this method should exist — why not just query units directly by `facility_required` JSON field?

---

## Research Questions

Answer these questions by reviewing the codebase and architecture:

### 1. **Intent & Use Case**
- [ ] What is `can_produce_locally?` supposed to do? Is it an:
  - Availability check (does this settlement have ANY unit that can produce X)?
  - Efficiency check (is local production cheaper than import)?
  - Location-specific check (some bodies can't produce certain resources)?
  - Something else?
- [ ] Who calls `can_produce_locally?` and why? (grep for callers)
- [ ] Is it actually needed or should procurement just query units directly by `facility_required` JSON?

### 2. **Resource Naming Convention**
- [ ] What's the canonical resource identifier in the backend?
  - Display names (`:oxygen`, `:water`)?
  - Chemical formulas (`:O2`, `:H2O`)?
  - Both (used in different contexts)?
- [ ] Where is the mapping defined (if it exists)?

### 3. **Unit Type Mapping by Location**
For each celestial body, list the actual unit types that produce each resource:

**Luna:**
- Oxygen: `regolith_oxygen_extractor` (or other types?)
- Water: `ice_processor`? (or `planetary_volatiles_extractor_mk1`?)
- Other resources: ?

**Mars:**
- Oxygen: ?
- Water: ?
- Regolith processing: ?

**Venus:**
- CO2: `atmospheric_processor`? (or different type?)
- Oxygen: `atmospheric_processor`? (or different type?)
- Nitrogen: ?

**Titan:**
- Methane: ?
- Nitrogen: ?
- Other volatiles: ?

### 4. **Is This Function Even Needed?**
- [ ] Look at how `facility_required` is used elsewhere in the codebase
- [ ] Could `can_produce_locally?` be replaced with a generic query like:
  ```ruby
  settlement.units.any? { |u| u.can_produce?(resource, location) }
  ```
- [ ] Or should it query by `facility_required` JSON field instead?

---

## Files to Review (Context Only — Gemini Cannot Access These)

**The following files exist in the codebase but are provided here for context only.**
Gemini should answer based on its knowledge of Galaxy Game architecture and any public documentation.

**Primary references (for context):**
- `galaxy_game/app/services/ai_manager/procurement_service.rb` — current stub implementation (lines 25-95)
- `galaxy_game/app/services/resource/acquisition.rb` — shows actual unit type checks (e.g., `regolith_processor`, `ice_processor`, `metal_processor`)
- `galaxy_game/spec/services/manufacturing/material_processing_service_spec.rb` — shows unit types in tests (e.g., `planetary_volatiles_extractor_mk1`)
- `galaxy_game/app/services/ai_manager/precursor_capability_service.rb` — shows `:oxygen`, `:water`, `:fuel`, `:metals` as capability symbols
- Blueprint JSON files in `data/json-data/blueprints/units/production/` — check `facility_required` fields

**Secondary context:**
- `galaxy_game/app/services/market/npc_price_calculator.rb` (line 213) — uses `has_facility?` (same design issue)
- `galaxy_game/app/models/settlement/base_settlement.rb` — verify `has_many :units` association

> **Note**: The code snippets in the "Problem Statement" section above are from the actual current codebase. Use them as reference for your analysis.

---

## Output Format

Provide your findings in a markdown report with:

1. **Intent & Recommendation**
   - What should `can_produce_locally?` actually do?
   - Should it exist or be replaced?

2. **Resource Naming Convention**
   - Canonical format (display names vs formulas)
   - Where mapping is defined

3. **Unit Type Mapping**
   - Table: Location × Resource → Unit Types (with actual code references)
   - Example:
     ```
     | Location | Resource | Unit Types | Evidence |
     |----------|----------|-----------|----------|
     | Luna | Oxygen | regolith_oxygen_extractor | resource/acquisition.rb line 421 |
     ```

4. **Recommendation for Refactor**
   - Proposed new implementation
   - Any architectural changes needed
   - Risk assessment

---

## Acceptance Criteria

- [ ] All 4 research questions answered with code evidence
- [ ] Unit type mapping table completed for all major bodies
- [ ] Clear recommendation on whether `can_produce_locally?` should exist
- [ ] If it should exist: proposed implementation provided
- [ ] References to actual code locations (file:line)

---

## Dependencies

**Blocks**: `2026-07-03-MEDIUM-REFACTOR-HAS-FACILITY-INTEGRATION.md`  
**Related**: `2026-07-03-MEDIUM-REFACTOR-HAS-FACILITY-INTEGRATION.md` (currently BLOCKED waiting for this research)

---

## Notes

This is a pure research task. No code changes. Just clarify the architecture so the refactor can proceed with correct design.
