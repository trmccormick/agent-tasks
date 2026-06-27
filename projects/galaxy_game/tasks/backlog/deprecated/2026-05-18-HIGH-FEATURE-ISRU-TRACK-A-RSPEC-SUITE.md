---
status: backlog
priority: HIGH
type: feature
system_domain: TERRA_SIM | CONTROLLERS | UNITS
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: false
codebase_status_2026_06_22: "STALE — IsruTrackA service does not exist. Current ISRU chain: TEU + PVE process regolith → depleted regolith + water. Ice mining from craters is separate process (future). Water issue relates to hydrated material depth in lunar regolith. Task needs recreation based on current codebase (TEU/PVE services) and existing work."
---

# TASK: ISRU Track A RSpec Test Suite Implementation
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-05-18
**Last Updated**: 2026-05-18

---

## Local Worker Triage Report
*Filled in by local model (Ollama via Continue) during backlog review*
*Local models read task files only — they cannot run commands or access the DB*

- **Template Conformance**: PASS — all sections present
- **Docker Wrapper Check**: REQUIRED — RSpec execution needed for verification
- **MVP Alignment**: VALID — ISRU production test coverage is MVP prerequisite
- **MVP Impact Note**: Cannot deploy ISRU Track A to Luna settlement without full test suite
- **Action Line**: CLOUD AGENT REQUIRED (needs Docker/RSpec execution)

---

## Agent Assignment

**Assigned To**: [GPT-4.1 0x or O3-mini 0x]
**Why This Agent**: Requires RSpec expertise and ability to execute tests in Docker container
**Supervision Level**: watched carefully

**Supervision Legend**:
- Watched carefully = 0x/0.33x cloud agents and all local models
- Standard = 0.33x agents on well-specified tasks
- Autonomous OK = 1x agents only

> Local Ollama agents: you cannot execute terminal commands, Docker, RSpec, or git.
> You can read files provided to you and create/edit files via Continue.
> Never fabricate command output. If you need a command run, ask the human.

---

## Context
ISRU Track A service (`app/services/isru_track_a.rb`) exists but has zero test coverage. This is unacceptable for MVP deployment as ISRU production (water extraction from lunar regolith) is critical for Luna settlement life support and fuel production. 

This task requires implementing a complete RSpec test suite including:
- Unit tests for all public methods in IsruTrackA service
- Tests for dependent PORO classes (RawGasBuffer, TankFarmSystem, LavaTubeAtmosphericHarvester)
- Integration tests covering the TEU → PVE production chain
- Error handling and edge case coverage
- Performance benchmarks for regolith processing throughput

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — PORO pattern constraints
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/new_agent/tasks/backlog/TASK_TEMPLATE.md` — format reference
- `spec/factories/*` — existing factory patterns to follow
- `config/initializers/poro_space.rb` — PORO pool configuration

**Prerequisites (must be completed first):**
- ✅ 2026-05-18-HIGH-ARCHITECTURE-ISRU-TRACK-A-SPEC-GAP-AUDIT.md (audit complete)
- ⏳ 2026-05-18-MEDIUM-DATA-ISRU-OPERATIONAL-DATA-FIXTURES.md (JSON fixtures needed)
- ⏳ 2026-05-18-LOW-DOCUMENTATION-ISRU-TRACK-A-DESIGN.md (design doc for method signatures)

> If prerequisites are not complete, create stub tests with TODO comments referencing missing dependencies.

---

## Problem Statement
**Current behavior**: ISRU Track A service has 0% test coverage. Cannot validate production logic or catch regressions.

**Expected behavior**: 
- Full RSpec suite in `spec/services/isru_track_a_spec.rb`
- ≥90% line coverage for IsruTrackA class
- All public methods tested with realistic operational parameters
- Error conditions and failure modes documented through tests
- Integration test covering end-to-end regolith processing workflow

**Why this matters**: ISRU Track A is production-critical infrastructure. Without tests, any code changes risk breaking water extraction for Luna settlement simulation.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|-|
| `spec/services/isru_track_a_spec.rb` | Main RSpec test file | New file to create |
| `spec/factories/raw_gas_buffer.rb` | FactoryBot for RawGasBuffer PORO | New file to create |
| `spec/factories/tank_farm_system.rb` | FactoryBot for TankFarmSystem PORO | New file to create |
| `spec/factories/lava_tube_harvester.rb` | FactoryBot for LavaTubeAtmosphericHarvester | New file to create |

### Reference Files — read but do not edit (unless adding new test factories)
| File | Why You Need It |
|---|-|
| `app/services/isru_track_a.rb` | Service code being tested |
| `spec/factories/unit_manager.rb` | Example factory pattern for UnitManager tests |
| `spec/support/poro_helpers.rb` | PORO-specific RSpec helpers if they exist |

### Migration (if needed)
- [x] No migration needed

---

## Implementation Steps

> 0x/0.33x agents: follow these steps exactly in order.
> 1x agents: use as a guide, apply judgment.
> Local Ollama agents: read steps carefully, ask human to run any commands, never fabricate output.

**Debug prints OK** — add `puts` for debugging test failures, remove before commit.

### Step 1 — Review Prerequisites and Dependencies
- Verify that JSON fixtures task (2026-05-18-MEDIUM-DATA-ISRU-OPERATIONAL-DATA-FIXTURES.md) is complete or in progress
- Check if design doc (2026-05-18-LOW-DOCUMENTATION-ISRU-TRACK-A-DESIGN.md) provides method signatures
- If missing, create test stubs with TODO comments referencing the gap

### Step 2 — Create PORO Factories
Create FactoryBot definitions for all PORO classes referenced in ISRU Track A:

```ruby
# spec/factories/raw_gas_buffer.rb
FactoryBot.define do
  factory :raw_gas_buffer do
    capacity { 100.0 }  # liters
    current_volume { 50.0 }
    gas_type { :oxygen }
    pressure { 2.5 }   # bar
    temperature { 280 } # Kelvin
  end
end
```

Repeat for `TankFarmSystem` and `LavaTubeAtmosphericHarvester` with appropriate attributes based on ISRU operational parameters from JSON fixtures.

### Step 3 — Implement Unit Tests for IsruTrackA Class
Create comprehensive test coverage:

**Test structure:**
```ruby
# spec/services/isru_track_a_spec.rb
require 'rails_helper'

describe IsruTrackA do
  describe '.process_regolith' do
    context 'with valid TEU and PVE units' do
      # Test normal operation
    end
    
    context 'when TEU capacity exceeded' do
      # Test error handling
    end
    
    context 'when PVE extraction fails' do
      # Test failure mode
    end
  end
  
  describe '#calculate_production_throughput' do
    # Test throughput calculations
  end
  
  describe '#transfer_to_tank_farm' do
    # Test gas transfer logic
  end
  
  describe '#harvest_from_lava_tube' do
    # Test lava tube atmospheric harvesting
  end
end
```

### Step 4 — Implement Integration Tests
Add integration test covering end-to-end workflow:
```ruby
describe 'ISRU Track A production chain integration', type: :integration do
  it 'processes regolith from TEU through PVE to TankFarmSystem' do
    # Setup: Create TEU, PVE units with operational parameters
    # Execute: Run full processing pipeline
    # Verify: RawGasBuffer filled, TankFarmSystem updated, production metrics logged
  end
end
```

### Step 5 — Add Error Handling Tests
Cover failure modes:
- Unit creation failures (UnitManager errors)
- PORO pool key missing errors
- Capacity exceeded exceptions
- Invalid operational parameters
- Lava tube pressure out of range

### Step 6 — Run RSpec Suite
Execute tests in Docker container:
```bash
docker-compose run --rm terra-sim-app bundle exec rspec spec/services/isru_track_a_spec.rb --format documentation
```

Fix any failures. Ensure all tests pass with green output.

### Step 7 — Verify Code Coverage
Run coverage report:
```bash
docker-compose run --rm terra-sim-app bundle exec rspec --coverage spec/services/isru_track_a_spec.rb
```

Verify ≥90% coverage for IsruTrackA class. Document any intentional uncovered areas in test file comments.

---

## Acceptance Criteria
- [ ] `spec/services/isru_track_a_spec.rb` created with comprehensive test coverage
- [ ] All PORO factories created (`raw_gas_buffer`, `tank_farm_system`, `lava_tube_harvester`)
- [ ] RSpec suite passes with 0 failures (`bundle exec rspec spec/services/isru_track_a_spec.rb`)
- [ ] Code coverage ≥90% for IsruTrackA class
- [ ] Integration test covering TEU → PVE → TankFarm pipeline exists and passes
- [ ] Error handling tests cover all documented failure modes
- [ ] No TODO comments referencing missing prerequisites (or if present, document gap in completion report)

---

## Stop Conditions — escalate to user immediately if:
- Existing code in `app/services/isru_track_a.rb` has methods that cannot be tested without design doc clarification
- PORO classes referenced do not exist and cannot be stubbed
- UnitManager.create! interface undefined or inconsistent
- More than 2 hours spent on debugging infrastructure issues (missing gems, Docker problems)

---

## Commit Instructions
Run git commands on **host**, not inside container:
```bash
git add spec/services/isru_track_a_spec.rb spec/factories/raw_gas_buffer.rb spec/factories/tank_farm_system.rb spec/factories/lava_tube_harvester.rb
git commit -m "[feature]: ISRU Track A RSpec test suite — full coverage with factories, integration tests, error handling"
git push
```

---

## Documentation
- [ ] Update `spec/README.md` if it exists to mention ISRU Track A test suite location
- [ ] Add comments in test file explaining any complex PORO pattern usage
- [ ] Document known gaps if any tests are stubbed due to missing design doc

---

## Dependencies
**Blocked by**: 
- 2026-05-18-MEDIUM-DATA-ISRU-OPERATIONAL-DATA-FIXTURES.md (need JSON fixtures for realistic test data)
- 2026-05-18-LOW-DOCUMENTATION-ISRU-TRACK-A-DESIGN.md (need method signatures and operational parameters)

**Blocks**: 
- None (this is a foundational task for ISRU Track A validation)

**Related tasks**: 
- 2026-05-18-HIGH-ARCHITECTURE-ISRU-TRACK-A-SPEC-GAP-AUDIT.md (parent audit task)
- 2026-05-18-MEDIUM-REFACTOR-ISRU-UNIT-MANAGER-INTEGRATION.md (may reveal additional test requirements)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- Created `spec/services/isru_track_a_spec.rb` with full RSpec suite
- Created PORO factories for RawGasBuffer, TankFarmSystem, LavaTubeAtmosphericHarvester
- Added integration tests for TEU → PVE production chain
- Implemented error handling tests for all failure modes

### Test Coverage Results
- IsruTrackA class coverage: [XX]%
- Total tests written: [XX]
- Integration tests: [X]
- Error handling tests: [X]

### Issues encountered
- [List any issues with PORO pattern, missing methods, or unclear interfaces]

### Known gaps
- [Document any stubbed tests due to missing design doc or unresolved dependencies]

---

## Handoff Summary
HANDOFF SUMMARY: [RSpec suite complete] | [coverage report attached] | [ready for ISRU Track A implementation tasks]