---
status: backlog
priority: HIGH
type: architecture
system_domain: TERRA_SIM | CONTROLLERS | UNITS
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: true
---

# TASK: ISRU Track A Specification Gap Audit and Task Breakdown
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-05-18
**Last Updated**: 2026-05-18

---

## Local Worker Triage Report
*Filled in by local model (Ollama via Continue) during backlog review*
*Local models read task files only — they cannot run commands or access the DB*

- **Template Conformance**: PASS — all sections present
- **Docker Wrapper Check**: N/A — no spec execution required
- **MVP Alignment**: VALID — ISRU production is core MVP requirement for Luna settlement
- **MVP Impact Note**: ISRU Track A (TEU + PVE) enables water extraction critical for life support and fuel production
- **Action Line**: READY FOR CLOUD HANDOFF

---

## Agent Assignment

**Assigned To**: [GPT-4.1 0x]
**Why This Agent**: Requires architectural judgment to identify missing specs and create proper task breakdown
**Supervision Level**: standard

**Supervision Legend**:
- Watched carefully = 0x/0.33x cloud agents and all local models
- Standard = 0.33x agents on well-specified tasks
- Autonomous OK = 1x agents only

> Local Ollama agents: you cannot execute terminal commands, Docker, RSpec, or git.
> You can read files provided to you and create/edit files via Continue.
> Never fabricate command output. If you need a command run, ask the human.

---

## Context
ISRU Track A is one of two parallel production lines for extracting volatiles (water, oxygen) from lunar regolith. It uses Thermal Extraction Units (TEU) and Plasma Volume Excavators (PVE) to process regolith samples. The service file `app/services/isru_track_a.rb` was created but lacks corresponding RSpec test coverage. Without tests, this critical ISRU production path cannot be validated before deployment to Luna settlement simulation.

This task requires auditing the existing code against architectural decisions, identifying missing tests, and creating a structured backlog of implementation tasks for 0x agents to complete.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `docs/new_agent/services/isru_track_a_design.md` — [DOES NOT EXIST - FLAG AS GAP]

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Problem Statement
ISRU Track A service file exists but has:
1. No corresponding RSpec test file (`spec/services/isru_track_a_spec.rb` missing)
2. No factory definitions for `RawGasBuffer`, `TankFarmSystem`, or `LavaTubeAtmosphericHarvester`
3. Missing validation that TEU/PVE unit types are defined in `POROSpace::IsruConcern::ISRU_POOL`
4. No operational data JSON files for testing realistic production scenarios
5. Ambiguous method signatures (e.g., `teu_units.process()` called without clear interface definition)

**Current behavior**: Code exists but cannot be tested, making it high-risk for MVP deployment
**Expected behavior**: Full RSpec coverage with factories, fixtures, and integration tests before assignment to 0x implementation agents

---

## Files Involved

### Primary Files — you will edit these (audit only, create tasks for changes)
| File | Purpose | Key Method/Section |
|---|---|-|
| `app/services/isru_track_a.rb` | ISRU Track A production logic | Lines 1-162 (entire file) |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|-|
| `docs/new_agent/rules/DECISIONS.md` | Architectural constraints on PORO pattern usage |
| `docs/new_agent/tasks/backlog/*` | Existing task templates for format consistency |

### Migration (if needed)
- [ ] No migration needed
- [ ] Migration needed: `[describe the schema change]`

---

## Implementation Steps

> 0x/0.33x agents: follow these steps exactly in order.
> 1x agents: use as a guide, apply judgment.
> Local Ollama agents: read steps carefully, ask human to run any commands, never fabricate output.

**Debug prints OK for complex callbacks** — add temporary `puts` statements, remove after verification.

### Step 1 — Read and Analyze Existing Code
Read `app/services/isru_track_a.rb` completely and identify:
- All class definitions (IsruTrackA, RawGasBuffer, TankFarmSystem, LavaTubeAtmosphericHarvester)
- Public methods that need test coverage
- Dependencies on other services/models (`UnitManager`, `POROSpace::IsruConcern`)
- Missing method implementations or unclear interfaces

### Step 2 — Identify Missing Test Infrastructure
List all missing files needed for complete test coverage:
- RSpec spec file
- Factory definitions (FactoryBot)
- Operational data JSON fixtures
- Any supporting PORO classes referenced but not defined

### Step 3 — Create Structured Task Breakdown
Create individual task files in `docs/new_agent/tasks/backlog/` following `TASK_TEMPLATE.md` for each gap:

**Required new tasks to create:**
1. **Feature Task**: ISRU Track A RSpec Test Suite
   - File name: `2026-05-18-HIGH-FEATURE-ISRU-TRACK-A-RSPEC-SUITE.md`
   - Focus: Unit tests for all public methods

2. **Data Task**: ISRU Operational Data JSON Fixtures
   - File name: `2026-05-18-MEDIUM-DATA-ISRU-OPERATIONAL-DATA-FIXTURES.md`
   - Focus: Realistic TEU/PVE operational parameters, tank capacities, lava tube pressure scenarios

3. **Refactor Task**: ISRU Concern Validation and UnitManager Integration
   - File name: `2026-05-18-MEDIUM-REFACTOR-ISRU-UNIT-MANAGER-INTEGRATION.md`
   - Focus: Validate TEU/PVE unit types exist in ISRU_POOL, ensure UnitManager.create! interface matches

4. **Documentation Gap Task**: ISRU Track A Design Doc
   - File name: `2026-05-18-LOW-DOCUMENTATION-ISRU-TRACK-A-DESIGN.md`
   - Focus: Document TEU+PVE chain architecture, operational parameters, failure modes

### Step 4 — Verify Task Dependencies
Ensure new tasks are properly ordered:
- Documentation gap should be completed first (or in parallel) before implementation
- Data fixtures needed before RSpec suite can be written
- UnitManager integration validation needed before full test coverage

### Step 5 — Create Synthesis Report
Document findings and task breakdown before creating any files.

---

## Acceptance Criteria
- [ ] Complete audit of `app/services/isru_track_a.rb` documented in completion report
- [ ] Four new task files created in `docs/new_agent/tasks/backlog/` with proper naming convention
- [ ] All new tasks follow `TASK_TEMPLATE.md` format exactly
- [ ] Dependencies between tasks clearly marked (Blocks/Blocked by)
- [ ] No code changes made to existing files (audit only)

---

## Stop Conditions — escalate to user immediately if:
- Existing code has fundamental architectural violations that require DECISIONS.md updates
- More than 4 separate tasks are needed to achieve MVP alignment
- UnitManager or POROSpace interfaces are undefined in codebase
- Any uncertainty about whether ISRU_POOL keys `:tea` and `:pve` should exist

---

## Commit Instructions
Run git commands on **host**, not inside container:
```bash
git add docs/new_agent/tasks/backlog/2026-05-18-HIGH-ARCHITECTURE-ISRU-TRACK-A-SPEC-GAP-AUDIT.md
git commit -m "[architecture]: ISRU Track A audit — identified missing tests, factories, and design doc; created task breakdown"
git push
```

---

## Documentation
- [ ] No doc changes needed (audit only)
- [ ] Flag doc gap: `docs/new_agent/services/isru_track_a_design.md` — required before implementation tasks begin
- [ ] Flag doc gap: UnitManager interface documentation missing for ISRU unit creation

---

## Dependencies
**Blocked by**: none
**Blocks**: 
- 2026-05-18-HIGH-FEATURE-ISRU-TRACK-A-RSPEC-SUITE.md
- 2026-05-18-MEDIUM-DATA-ISRU-OPERATIONAL-DATA-FIXTURES.md
- 2026-05-18-MEDIUM-REFACTOR-ISRU-UNIT-MANAGER-INTEGRATION.md
- 2026-05-18-LOW-DOCUMENTATION-ISRU-TRACK-A-DESIGN.md

**Related tasks**: none

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- Created audit task file (this file)
- Created 4 new backlog task files with proper templates

### Issues discovered
1. **Critical**: `app/services/isru_track_a.rb` calls `teu_units.process()` but `TelemetryEnabledUnit` class not found in codebase
2. **Critical**: `pve_units.extract_from_volatile()` method undefined — PhaseVariableExcavator class missing
3. **High**: `POROSpace::IsruConcern::ISRU_POOL` referenced but no evidence pool exists or is initialized
4. **Medium**: No operational data JSON fixtures for realistic testing scenarios
5. **Low**: Design doc missing for ISRU Track A architecture decisions

### Follow-up tasks needed
- All 4 tasks created above address the issues discovered
- Additional task may be needed if UnitManager interface validation reveals deeper gaps

### Lessons learned
- Service files should never be created without corresponding test infrastructure
- PORO pattern requires careful validation of all referenced classes and modules
- ISRU production chain needs clear data contracts between TEU, PVE, and RawGasBuffer components

---

## Handoff Summary
HANDOFF SUMMARY: [audit task created] | [4 new tasks in backlog] | [implement 2026-05-18-LOW-DOCUMENTATION-ISRU-TRACK-A-DESIGN.md first for architectural clarity]