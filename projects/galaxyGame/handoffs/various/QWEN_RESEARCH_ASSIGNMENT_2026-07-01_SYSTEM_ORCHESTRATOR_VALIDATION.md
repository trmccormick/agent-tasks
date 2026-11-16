# Qwen Research Assignment: SystemOrchestrator Validation & Task Reconciliation

**Date**: 2026-07-01  
**Agent Role**: Research/Architecture Reviewer  
**Project**: galaxy_game  
**Topic**: Validate Gemini's SystemOrchestrator task proposals against actual codebase state and gap analysis

---

## MANDATORY READS
1. `/Users/tam0013/Documents/git/agent-tasks/README.md`
2. `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md`
3. `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/REVIEW_AGENT_GUIDE.md`
4. **Gemini's work** (output from 2026-06-30 session):
   - `docs/new_agent/projects/galaxy_game/handoffs/2026-06-30-1156-GEMINI-SESSION_HANDOFF_SUMMARY.md`
   - `docs/new_agent/projects/galaxy_game/summaries/2026-06-30-RESEARCH-SYSTEM-ORCHESTRATOR-AUDIT.md`
   - `docs/new_agent/projects/galaxy_game/summaries/2026-06-30-GAP-ANALYSIS-PROPOSED-TASKS.md`

---

## YOUR TASK

**DO NOT IMPLEMENT.** Your job is to **research, audit, and validate** — then propose a concrete task-file structure for the next phase.

### Stage 1: Validate Existing Proposals

**Read Gemini's Gap Analysis** (`2026-06-30-GAP-ANALYSIS-PROPOSED-TASKS.md`) and verify against actual code:

1. **ResourceAllocator dual-constructor claim** (Gap #3):
   - Does `resource_allocator.rb` actually have two different constructor signatures?
   - Does the existing `resource_allocator_spec.rb` test the OLD single-settlement pattern?
   - Would merging constructors break existing specs? (simulate, don't run)

2. **Referenced spec files** (Gap #2):
   - Confirm these do NOT exist:
     - `settlement_manager_spec.rb`
     - `system_state_spec.rb`
     - `strategic_planner_spec.rb`
   - Confirm this DOES exist:
     - `resource_allocator_spec.rb`

3. **PriorityArbitrator** (Gap #4):
   - File: `galaxy_game/app/services/ai_manager/priority_arbitrator.rb`
   - Does a spec file exist? If not, quote the public interface methods that need testing.

4. **LogisticsCoordinator** (Gap #5):
   - File: `galaxy_game/app/services/ai_manager/logistics_coordinator.rb`
   - Does a spec file exist? If not, list the main responsibilities from the code.
   - Are transport capacities really hardcoded? (quote the code)

### Stage 2: Assess Missing Coverage

Audit the 4 gaps Gemini marked as NOT addressed:

5. **Multi-settlement integration test** (Gap #1):
   - Current integration spec: `spec/services/ai_manager/manager_system_orchestrator_integration_spec.rb`
   - Is it "heavily stubbed"? (check fixtures and mocks)
   - What would a real 2+ settlement integration test need to verify?

6. **Financial integration** (Gap #6):
   - Does `Financial::Transaction` exist? (check `galaxy_game/app/services/financial/` or `galaxy_game/app/models/`)
   - Are settlement accounts real? (check `SettlementCore` for `gcc_account`)
   - Quote the expected transaction format for a resource transfer.

7. **Wormhole route integration** (Gap #7):
   - Current hardcoded values in `LogisticsCoordinator`: quote them.
   - Do `WormholeManager` and `NetworkOptimizer` exist? (file paths)
   - What methods would `LogisticsCoordinator` need to call to get real route data?

8. **EconomicForecasterService wiring** (Gap #8):
   - Does `EconomicForecasterService` exist? (file path)
   - Is it currently used anywhere? (grep for references)
   - What would it need to integrate into SystemOrchestrator?

### Stage 3: Dependency Graph

**Map task execution order:**
- Which tasks must run sequentially (Task 2 depends on Task 1, etc.)?
- Which can run in parallel?
- Where are the risky transitions (mock → real data)?

---

## REQUIRED: STATUS SYNTHESIS REPORT

**Create this before recommending changes** — save to:
```
/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
2026-07-01-RESEARCH-SYSTEM-ORCHESTRATOR-VALIDATION.md
```

### Report Sections

1. **Scope Assessment**
   - Codebase files audited (list paths)
   - Validation results for each of Gemini's 9 gaps (✅ confirmed / ❌ refuted / ⚠️ needs clarification)

2. **Proposed Task Structure**
   - How many tasks should actually exist? (4 existing + ? new)
   - List with explicit dependencies:
     ```
     1. ResourceAllocator Fix (prerequisite for Task 2)
     2. SettlementManager Integration (requires Task 1)
     3. SystemState Persistence (can run parallel to 1-2)
     4. StrategicPlanner Extraction (requires Task 3)
     5. [NEW if needed] Multi-Settlement Integration Test (final)
     6. [NEW if needed] Spec Coverage for PriorityArbitrator + LogisticsCoordinator
     7. [NEW if needed] Financial Integration (deferred or low-priority?)
     ```

3. **Risk Assessment**
   - Which tasks carry nil-related or factory-stub risks?
   - What mock data still exists that must be replaced?
   - Estimate sprint scope: which tasks are 1-session feasible? which need chunking?

4. **Concrete Recommendations**
   - Should Gemini's 4 tasks be modified? (what changes)
   - Which gaps become new tasks vs. deferred?
   - Final task execution order with explicit prerequisite chains

---

## DELIVERABLE

**You must provide:**

- ✅ Research report (saved to git-staged) with all 4 validation stages completed
- ✅ Concrete task-file recommendations (don't write full task files yet — just outline what each should contain)
- ✅ Dependency graph (text or ASCII diagram showing execution order)
- ✅ Estimate: how many sessions to complete all proposed tasks at current velocity?

---

## PROCESS

1. **Read all mandatory files above**
2. **Do NOT move any task files to active/** — this is research only
3. **Do git add your synthesis report** — save to `/Users/tam0013/Documents/git/agent-tasks/` path above
4. **Paste synthesis report here** before making any recommendations
5. **Wait for human approval** on task structure before proceeding

---

## SUCCESS CRITERIA

- [ ] All 9 gaps explicitly addressed in report (confirmed or refuted with evidence)
- [ ] Concrete task count and dependencies documented
- [ ] Risk mitigation for mock-to-real transitions identified
- [ ] Report is thorough enough that Claude (review agent) can make final approval decision
- [ ] No implementation changes — research and recommendations only

