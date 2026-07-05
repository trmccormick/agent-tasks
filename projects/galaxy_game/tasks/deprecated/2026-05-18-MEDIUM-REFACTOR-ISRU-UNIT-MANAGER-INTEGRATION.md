---
status: backlog
priority: MEDIUM
type: refactor
system_domain: TERRA_SIM | CONTROLLERS | UNITS
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: false
codebase_status_2026_06_22: "STALE — Part of incomplete ISRU Track A work cycle. IsruTrackA service does not exist. Task assumes non-existent service and needs recreation based on current TEU/PVE implementations. Related: 2026-05-18-HIGH-FEATURE-ISRU-TRACK-A-RSPEC-SUITE.md, 2026-05-18-MEDIUM-DATA-ISRU-OPERATIONAL-DATA-FIXTURES.md"
---

# TASK: ISRU Track A Unit Manager Integration Refactor
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: refactor
**Created**: 2026-05-18
**Last Updated**: 2026-05-18

---

## Local Worker Triage Report
*Filled in by local model (Ollama via Continue) during backlog review*
*Local models read task files only — they cannot run commands or access the DB*

- **Template Conformance**: PASS — all sections present
- **Docker Wrapper Check**: REQUIRED — needs Rails console or RSpec execution to verify integration
- **MVP Alignment**: VALID — UnitManager interface must be correct for ISRU production
- **MVP Impact Note**: Cannot proceed with ISRU implementation if UnitManager integration is broken
- **Action Line**: REQUIRES CLOUD AGENT (command execution needed)

---

## Agent Assignment

**Assigned To**: GPT-4.1 0x, O3-mini 0x, or Claude 1.5 0.33x
**Why This Agent**: Requires command execution to run tests and verify UnitManager integration
**Supervision Level**: standard

**Supervision Legend**:
- Watched carefully = 0x/0.33x cloud agents and all local models
- Standard = 0.33x agents on well-specified tasks
- Autonomous OK = 1x agents only

> This task requires Rails console or RSpec execution — cannot be completed by local Ollama.

---

## Context
ISRU Track A needs to integrate with the existing UnitManager system that controls TerraSim units. The current implementation may have gaps:
- Missing method signatures for TEU and PVE unit control
- Incomplete state transitions between operational modes
- Lack of error handling for unit communication failures
- Unclear interface for querying unit status and production metrics

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — UnitManager integration patterns
- `app/controllers/unit_manager_controller.rb` (or equivalent) — existing UnitManager implementation
- `config/initializers/poro_space.rb` — how units are loaded into PORO pools
- `app/services/isru_track_a.rb` — understand what ISRU Track A expects from UnitManager

**Existing Unit Patterns**:
Look for other unit implementations to follow the same pattern (e.g., LifeSupport, PowerGeneration).

---

## Problem Statement
**Current behavior**: ISRU Track A service may be calling UnitManager methods that don't exist or have incompatible signatures. No integration tests verify the UnitManager interface works correctly with TEU and PVE units.

**Expected behavior**:
- All ISRU Track A UnitManager method calls are properly implemented
- State transitions (idle → active → maintenance → error) work correctly
- Error handling covers all failure modes (communication loss, hardware faults, capacity limits)
- Production metrics can be queried and reported accurately

**Why this matters**: Without proper UnitManager integration, ISRU Track A cannot control actual hardware or report production data during Luna deployment.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|-|
| `app/controllers/unit_manager_controller.rb` (or equivalent) | Add ISRU-specific unit methods | New methods for TEU/PVE control |
| `app/services/isru_track_a.rb` | Update to use correct UnitManager interface | Method calls to UnitManager |
| `app/models/unit.rb` (if exists) | Add ISRU unit types and states | Unit type constants, state machine |
| `spec/requests/unit_manager_spec.rb` | Integration tests for UnitManager API | RSpec requests tests |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|-|
| `app/controllers/life_support_controller.rb` (or equivalent) | Example of existing unit integration pattern |
| `config/initializers/poro_space.rb` | How units are initialized and loaded |
| `app/services/isru_track_a.rb` | Understand expected UnitManager interface |

### Migration (if needed)
- [ ] May need migration if adding new database columns for ISRU unit states

---

## Implementation Steps

> 0x/0.33x agents: follow these steps exactly in order.
> 1x agents: use as a guide, apply judgment.

### Step 1 — Audit Current UnitManager Interface
1. Search for all `UnitManager` method definitions:
   ```bash
   grep -r "def.*unit" app/controllers/ | head -20
   ```
2. Identify existing unit types and their state machines
3. Document current ISRU Track A UnitManager calls in `app/services/isru_track_a.rb`
4. List any missing methods or incompatible signatures

### Step 2 — Define ISRU Unit Types and States
Add ISRU-specific constants to `app/models/unit.rb` (or equivalent):
```ruby
class Unit
  # Existing unit types...
  
  # ISRU Track A unit types
  TEU = 'thermal_extraction_unit'
  PVE = 'plasma_volume_excavator'
  RAW_GAS_BUFFER = 'raw_gas_buffer'
  TANK_FARM = 'tank_farm_system'
  LAVA_TUBE_HARVESTER = 'lava_tube_atmospheric_harvester'
  
  # State constants
  STATES = [:idle, :active, :maintenance, :error, :standby].freeze
end
```

### Step 3 — Implement Missing UnitManager Methods
Add methods to `app/controllers/unit_manager_controller.rb` (or equivalent):
```ruby
def initialize_isru_units(unit_types)
  # Initialize TEU, PVE, etc. with default parameters
  unit_types.each do |type|
    create_unit(type: type, initial_state: :idle)
  end
end

def set_teu_operating_params(tau_id, temperature, pressure, throughput)
  # Update TEU operating parameters
  update_unit_parameters(tau_id, {temperature: temperature, pressure: pressure, throughput: throughput})
end

def start_pve_extraction(pve_id)
  # Transition PVE to active state and begin extraction
  transition_unit_state(pve_id, :active)
end

def query_production_metrics(unit_ids)
  # Return production data for specified units
  unit_ids.map { |id| get_unit_metrics(id) }
end
```

### Step 4 — Update ISRU Track A Service
Modify `app/services/isru_track_a.rb` to use the correct UnitManager interface:
```ruby
class IsruTrackA
  def initialize
    @unit_manager = UnitManager.new
  end
  
  def setup_units
    @unit_manager.initialize_isru_units([
      Unit::TEU,
      Unit::PVE,
      Unit::RAW_GAS_BUFFER,
      Unit::TANK_FARM
    ])
  end
  
  def start_production(temperature, pressure)
    teu_id = get_unit_id(Unit::TEU)
    @unit_manager.set_teu_operating_params(teu_id, temperature, pressure, nil)
    @unit_manager.start_pve_extraction(get_unit_id(Unit::PVE))
  end
end
```

### Step 5 — Add Integration Tests
Create `spec/requests/unit_manager_isru_integration_spec.rb`:
```ruby
RSpec.describe UnitManagerController, type: :request do
  describe 'ISRU Track A integration' do
    it 'initializes all ISRU units' do
      post '/unit_manager/initialize_isru_units', params: {
        unit_types: [Unit::TEU, Unit::PVE]
      }
      expect(response).to have_http_status(:ok)
    end
    
    it 'sets TEU operating parameters' do
      # Test setting temperature, pressure, throughput
    end
    
    it 'starts PVE extraction' do
      # Test state transition to active
    end
    
    it 'handles unit communication failures' do
      # Test error handling for network timeouts, hardware faults
    end
  end
end
```

### Step 6 — Run Integration Tests
Execute tests to verify integration:
```bash
docker-compose exec terra_sim bundle exec rspec spec/requests/unit_manager_isru_integration_spec.rb --format documentation
```

---

## Acceptance Criteria
- [ ] All ISRU Track A UnitManager method calls are implemented
- [ ] TEU and PVE unit types added to Unit model
- [ ] State transitions work correctly for all ISRU units
- [ ] Error handling covers communication failures and hardware faults
- [ ] Integration tests pass (RSpec)
- [ ] No breaking changes to existing UnitManager functionality

---

## Stop Conditions — escalate to user immediately if:
- UnitManager interface is fundamentally different from expected pattern after 1 hour of investigation
- More than 2 hours spent on refactoring without making progress
- Existing tests break and cannot be fixed within 30 minutes

---

## Commit Instructions
Run git commands on **host**, not inside container:
```bash
git add app/controllers/unit_manager_controller.rb app/services/isru_track_a.rb app/models/unit.rb spec/requests/unit_manager_isru_integration_spec.rb
git commit -m "[refactor]: ISRU Track A UnitManager integration — added TEU/PVE unit types, state transitions, error handling"
git push
```

---

## Documentation
- [ ] Add inline comments explaining ISRU-specific unit parameters
- [ ] Update `docs/new_agent/rules/DECISIONS.md` with ISRU unit integration patterns

---

## Dependencies
**Blocked by**: 
- 2026-05-18-HIGH-ARCHITECTURE-ISRU-TRACK-A-SPEC-GAP-AUDIT.md (parent audit task)
- 2026-05-18-Low-DATA-ISRU-OPERATIONAL-DATA-FIXTURES.md (may need operational parameters for testing)

**Blocks**: 
- None (foundational integration task)

**Related tasks**: 
- 2026-05-18-HIGH-FEATURE-ISRU-TRACK-A-RSPEC-SUITE.md (RSpec suite may reveal additional requirements)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- Added ISRU unit types to Unit model
- Implemented missing UnitManager methods for TEU/PVE control
- Updated ISRU Track A service to use correct interface
- Created integration tests

### Test Results
- Integration tests passing: [Y/N]
- Coverage of new code: [XX]%

### Issues encountered
- [Any problems with existing UnitManager patterns or unexpected behaviors]

---

## Handoff Summary
HANDOFF SUMMARY: [UnitManager integration complete] | [tests passing] | [ready for ISRU Track A implementation]