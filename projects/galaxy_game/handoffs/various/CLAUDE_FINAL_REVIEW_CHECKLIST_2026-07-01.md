# Claude Final Review Checklist: SystemOrchestrator Task Set

**Date**: 2026-07-01  
**Agent Role**: Architecture/Planning Review  
**Project**: galaxy_game  
**Review Trigger**: After Qwen completes research validation and proposes task structure

---

## PROCESS

**You will receive this checklist AFTER:**
1. ✅ Gemini completed initial scope definition (done 2026-06-30)
2. ✅ Qwen completed research validation (produces synthesis report 2026-07-01)

**Your job**: Final architecture + feasibility sign-off before tasks move to `active/` folder for implementation.

---

## REVIEW CHECKLIST

### 1. Scope Completeness
- [ ] All 9 gaps from Gemini's analysis are addressed in proposed tasks (not deferred)
  - ⚠️ If gaps are deferred, is there explicit justification? (e.g., "low priority, next sprint")
- [ ] Task count is reasonable for current sprint velocity (estimate from Qwen's report)
- [ ] No hidden dependencies discovered by Qwen that change execution order

### 2. Risk Assessment
- [ ] **Nil-related failures**: Are there transitions from mock data → real objects that need factory setup?
  - Example: `SettlementManager` moves from hardcoded hash to real `BaseSettlement` data
  - Does the task include factory fixture setup or migration steps?
- [ ] **Test coverage**: Do all tasks include explicit "create spec" as step 1?
  - Are 3 missing spec files (`settlement_manager_spec.rb`, `system_state_spec.rb`, `strategic_planner_spec.rb`) explicitly planned?
- [ ] **Circular dependencies**: Can Task 2 execute before Task 1? (should be prevented by explicit ordering)

### 3. Task File Quality
- [ ] Each task file includes:
  - Clear 3-5 sentence objective
  - Explicit prerequisites/dependencies
  - "Create spec" as step 1 (or explain why not)
  - Synthesis review requirement (before implementation)
  - Concrete success criteria (spec green, X examples pass, etc.)
- [ ] Task filenames follow format: `2026-07-01-HIGH/MEDIUM/LOW-[CATEGORY]-[DESCRIPTION].md`
- [ ] YAML header includes `status: backlog` (ready to move to active)

### 4. Architectural Coherence
- [ ] Tasks maintain clear layer separation:
  - Data layer (SystemState persistence) ← independence OK
  - Coordination layer (ResourceAllocator, SettlementManager, LogisticsCoordinator)
  - Strategy layer (StrategicPlanner)
  - Integration layer (multi-settlement tests)
- [ ] No task tries to do too much (e.g., "refactor ResourceAllocator AND extract StrategicPlanner" = 2 tasks)
- [ ] Mock data removal plan is explicit (don't just say "use real data" — specify which methods/fixtures change)

### 5. Test Strategy
- [ ] Integration test (multi-settlement) is positioned as FINAL task (after all coordination components ready)
- [ ] Are existing specs that will break documented? (e.g., `resource_allocator_spec.rb` tests old constructor)
- [ ] Baseline before task sequence: is `manager_system_orchestrator_integration_spec.rb` known-passing?
  - If not, note as blocker — don't proceed until baseline green

### 6. Scope Lock
- [ ] Deferred items explicitly listed (don't just omit them):
  - Financial integration? (deferred, why?)
  - Wormhole routing? (deferred, why?)
  - EconomicForecaster wiring? (deferred, why?)
- [ ] If anything changes from Gemini's proposal, document the change reason

### 7. Feasibility
- [ ] Is this realistic for a 1-week sprint at current velocity? (or is it 2-week / 3-week scope?)
- [ ] Are there any "unknown unknowns" (new factories, missing methods, DB schema changes)?
- [ ] Is there a fallback if a task uncovers show-stoppers (e.g., "if migration breaks > 3 tests, defer to next sprint")?

---

## APPROVAL DECISION TREE

**If all checks ✅:**  
→ **APPROVE** — Mark tasks ready for activation. Gemini moves files to `active/` folder, updates status.md.

**If minor issues (1-3 checks ⚠️):**  
→ **CONDITIONAL APPROVE** — Document required changes, send back to Qwen or Gemini for fix, re-submit.

**If major issues (> 3 checks ⚠️ or > 1 blocker):**  
→ **REJECT** — Request rework. Do NOT proceed with implementation. Document blockers, loop back to research phase.

---

## REQUIRED BEFORE APPROVAL

- [ ] Qwen's synthesis report pasted here (full text or link)
- [ ] Proposed task files attached or linked (at least outline/summary of each)
- [ ] Dependency graph (text or diagram) showing execution order
- [ ] Sprint estimate (how many days/weeks to complete)
- [ ] Risk mitigation plan for any identified issues

---

## SIGN-OFF

**Claude Reviewer:**  
- [ ] All checklist items reviewed
- [ ] Decision recorded (APPROVE / CONDITIONAL APPROVE / REJECT)
- [ ] Blockers or conditions documented
- [ ] Next action assigned (Gemini activates tasks, or Qwen refines research)

**Date Approved**: _______________  
**Notes**: _______________

