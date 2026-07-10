# Phase Placement Audit: SystemOrchestrator Task Decomposition

**Date**: 2026-06-30
**Audit Scope**: Verify correct phase folder placement for 8 decomposed tasks
**Reference**: `PHASE_STRUCTURE.md`, `01_story_arc.md`, `10_implementation_phases.md`

---

## Phase Classification Review

### Original Task Metadata
**File**: `docs/new_agent/projects/galaxy_game/tasks/review/systemorchestrator_development.md`
```
phase: 8
created: 2026-06-21
status: backlog
```

### Phase Structure Reference (from attachments)

**Phase 5**: SystemOrchestrator (Integration) & Calibration Prep
- **Goal**: Test and tune Luna simulation to prove fuel loop viability before any multi-system expansion begins
- **Purpose**: Run simulation, watch market emergence and stockpile accumulation
- **Description**: "This phase is NOT about building new features. It's about running Luna simulation with current AI Manager logic to identify gaps in fuel loop viability"
- **Key Deliverable**: SystemOrchestrator integration for calibration work
- **Status**: Ready for implementation as calibration work
- **Gate Condition**: Requires completion of System A Phases 1–4 ✅

**Phase 8**: Orbital Construction & Craft (NOT System Orchestration)
- **Goal**: Test shipyard construction and large spacecraft options
- **Focus**: L1 shipyard construction, orbital manufacturing, tug/cycler craft
- **Key Deliverables**: Shipyard options, heavy lift transport, cycler construction
- **Gate Condition**: Requires Phase 6 infrastructure in place

**Phase 9**: Mars Foothold Expansion (runs in parallel with Phase 10, 11, 12, 13)
- **Focus**: Mars orbital + surface settlement with multi-option testing
- **Key Deliverables**: Phobos/Deimos repositioning, surface settlement, terraforming initiation

---

## Current File Placement

**Actual Location**: `docs/new_agent/projects/galaxy_game/tasks/proposed new tasks/2026-06-30-TASK-UPDATE-1/`

**8 Files Placed**:
1. 2026-06-30-HIGH-REFACTOR-RESOURCE-ALLOCATOR-STABILITY.md
2. 2026-06-30-HIGH-DATA-SETTLEMENT-MANAGER-INTEGRATION.md
3. 2026-06-30-HIGH-INTEGRATION-MULTI-SETTLEMENT-ORCHESTRATOR-SPEC.md
4. 2026-06-30-MEDIUM-ARCHITECTURE-SYSTEM-STATE-PERSISTENCE.md
5. 2026-06-30-MEDIUM-REFACTOR-STRATEGIC-PLANNER-EXTRACTION.md
6. 2026-06-30-MEDIUM-TEST-PRIORITY-ARBITRATOR-COVERAGE.md
7. 2026-06-30-MEDIUM-TEST-LOGISTICS-COORDINATOR-COVERAGE.md
8. 2026-06-30-LOW-PHASE-8B-DEFERRAL.md

---

## Analysis: Correct Phase Placement

### Task Classification Against Phase Structure

**All 8 tasks are PHASE 5 work**, not Phase 8 or phase8+:

| Task | Content | Phase Classification | Reasoning |
|---|---|---|---|
| Task 1: ResourceAllocator Stability | Fix dual-constructor bug in ResourceAllocator service | **Phase 5** | Core SystemOrchestrator component; needed for orchestration to function |
| Task 2: Settlement Manager Real Data | Wire SettlementManager to real BaseSettlement inventory | **Phase 5** | Enables SystemOrchestrator to work with real data vs. mocks |
| Task 3: Multi-Settlement Integration Spec | Integration test with 2+ settlements competing for resources | **Phase 5** | Validates SystemOrchestrator multi-settlement coordination (core Phase 5 gate criterion) |
| Task 4: System State Persistence | Add Rails.cache persistence for system-wide state | **Phase 5** | Enables monitoring across ticks; required for "system health monitoring" acceptance criterion |
| Task 5: Strategic Planner Extraction | Extract strategic planning into testable StrategicPlanner class | **Phase 5** | Enables "long-term strategic planning capabilities" acceptance criterion |
| Task 6: PriorityArbitrator Coverage | Comprehensive tests for conflict resolution logic | **Phase 5** | Validates "settlement priority ranking and conflict resolution" acceptance criterion |
| Task 7: LogisticsCoordinator Coverage | Comprehensive tests for transfer optimization | **Phase 5** | Validates "inter-body logistics coordination" acceptance criterion |
| Task 8: Phase 8B Deferral | Explicit deferral of wormhole routing, economics wiring | **Phase 5 Documentation** | Prevents scope creep during Phase 5; clarifies what Phase 8+ actually includes |

### Why Phase 5, Not Phase 8?

**Phase 5 Definition**: "SystemOrchestrator (Integration) & Calibration Prep"
- Focus: Running Luna simulation with current AI Manager logic
- Activity: Identifying gaps in fuel loop viability
- Deliverable: SystemOrchestrator integration for calibration work

**Phase 8 Definition**: "Orbital Construction & Craft"
- Focus: Shipyard construction, craft manufacturing (tugs, cyclers)
- Activity: L1 station buildout, heavy lift transport
- Deliverable: Functional orbital production infrastructure

**Mismatch**: Original task file labeled these as `phase: 8`, but content is clearly Phase 5 (SystemOrchestrator integration, calibration prep).

---

## Correct Folder Structure

### Current State
```
docs/new_agent/projects/galaxy_game/tasks/
├── backlog/
│   ├── phase5+/          ← WHERE THESE TASKS BELONG
│   ├── phase6+/
│   ├── phase7+/
│   ├── phase8+/          ← NOT HERE
│   ├── phase9+/
│   └── ...
└── proposed new tasks/
    └── 2026-06-30-TASK-UPDATE-1/    ← CURRENTLY HERE (incorrect)
        ├── 8 task files
        └── summaries/
```

### Recommended Placement
```
docs/new_agent/projects/galaxy_game/tasks/backlog/phase5+/
├── 2026-06-30-HIGH-REFACTOR-RESOURCE-ALLOCATOR-STABILITY.md
├── 2026-06-30-HIGH-DATA-SETTLEMENT-MANAGER-INTEGRATION.md
├── 2026-06-30-HIGH-INTEGRATION-MULTI-SETTLEMENT-ORCHESTRATOR-SPEC.md
├── 2026-06-30-MEDIUM-ARCHITECTURE-SYSTEM-STATE-PERSISTENCE.md
├── 2026-06-30-MEDIUM-REFACTOR-STRATEGIC-PLANNER-EXTRACTION.md
├── 2026-06-30-MEDIUM-TEST-PRIORITY-ARBITRATOR-COVERAGE.md
├── 2026-06-30-MEDIUM-TEST-LOGISTICS-COORDINATOR-COVERAGE.md
├── 2026-06-30-LOW-PHASE-8B-DEFERRAL.md
└── TASK_REFACTOR_SUMMARY.md
```

---

## Audit Findings

### Current State Issues
1. ❌ Tasks placed in `proposed new tasks/` instead of `backlog/phase5+/`
2. ❌ Original task metadata lists `phase: 8` but content is Phase 5
3. ❌ No distinction between Phase 5 SystemOrchestrator tasks and Phase 8 orbital manufacturing tasks
4. ⚠️ Supporting audit/gap analysis documents not in phase-aware location

### What Tasks Actually Address

**Phase 5 Acceptance Criteria** (from documentation):
- ✅ SystemOrchestrator integration (Tasks 1-5)
- ✅ Multi-settlement coordination (Task 3)
- ✅ Priority frameworks and decision-making (Task 6)
- ✅ Inter-body logistics (Task 7)
- ✅ System health monitoring (Task 4)
- ✅ Strategic planning capabilities (Task 5)
- ✅ Resource allocation algorithms (Tasks 1-2)

**Phase 8 vs. Phase 5 Clarity**:
- **Task 8 (Phase 8B Deferral)** explicitly states: "Do NOT add Phase 8 features (shipyards, craft, orbital manufacturing) to Phase 5 scope"
- This prevents accidental scope creep where orbital manufacturing gets mixed into SystemOrchestrator calibration work

---

## Recommended Action

### For Planning/Governance
1. **Correct original task metadata**: Update `systemorchestrator_development.md` to specify `phase: 5` (not `phase: 8`)
2. **Move tasks to correct folder**: Relocate 8 files from `proposed new tasks/2026-06-30-TASK-UPDATE-1/` to `backlog/phase5+/`
3. **Update supporting docs location**: Move audit and gap analysis to `backlog/phase5+/summaries/` or similar

### For Agent Dispatch
1. Tasks are ready for implementation once moved to `phase5+/` folder
2. Execution sequence: Task 1 → Task 2 → Task 4 → Task 5 (sequential); Tasks 6-7 parallel; Task 3 validates all
3. Execution timeline: 8-14 hours total effort (6-8 hours with 2 concurrent agents)

### For Phase 8 Distinction
1. **Phase 8 tasks** (when ready): Focus on L1 shipyard, orbital manufacturing, craft construction
2. **Phase 5 tasks** (current): Focus on SystemOrchestrator integration and calibration
3. Keep these distinct in folder structure to prevent accidental mixing

---

## Conclusion

**Audit Result**: ✅ Tasks are correctly decomposed and prioritized, but **placed in wrong folder**.

**Correct Placement**: `backlog/phase5+/` (SystemOrchestrator Integration & Calibration Prep)

**Not**: `proposed new tasks/` or `phase8+/` (which is Orbital Construction & Craft)

**Status**: Ready to move when governance confirms. All tasks are Phase 5 work addressing SystemOrchestrator integration per the phase structure documentation.
