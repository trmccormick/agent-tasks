# SystemOrchestrator Refactor — Multi-Agent Review Workflow

**Date**: 2026-07-01  
**Coordinated Review Process**: 3-Agent Pipeline  
**Goal**: Validate task scope, identify gaps, create implementation-ready task files

---

## WORKFLOW OVERVIEW

```
┌─────────────────────────────────────────────────────────────────┐
│  PHASE 1: Gemini's Initial Work (COMPLETED 2026-06-30)         │
│  • Scope definition + gap analysis                              │
│  • 4 proposed tasks drafted                                     │
│  • Output: Session handoff + 2 research documents               │
└─────────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────────┐
│  PHASE 2: Qwen Research & Validation (START HERE)               │
│  • Audit Gemini's proposals against actual codebase             │
│  • Validate all 9 identified gaps                               │
│  • Create concrete task structure recommendations               │
│  • Produce: Synthesis report with all findings                  │
│  📄 Handoff: QWEN_RESEARCH_ASSIGNMENT_2026-07-01_*              │
└─────────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────────┐
│  PHASE 3: Claude Final Review & Approval                        │
│  • Review Qwen's findings                                       │
│  • Check task quality + completeness                            │
│  • Verify risk mitigation plan                                  │
│  • Approve or request rework                                    │
│  • 📄 Checklist: CLAUDE_FINAL_REVIEW_CHECKLIST_2026-07-01       │
└─────────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────────┐
│  PHASE 4: Task Activation (After Approval)                      │
│  • Gemini moves approved tasks to active/ folder                │
│  • Update YAML: status: backlog → status: active               │
│  • Update status.md with in-progress note                       │
│  • Implementation agents pick up tasks                          │
└─────────────────────────────────────────────────────────────────┘
```

---

## PHASE 2: QWEN RESEARCH ASSIGNMENT

**Status**: Ready to dispatch  
**Location**: `docs/new_agent/projects/galaxy_game/handoffs/QWEN_RESEARCH_ASSIGNMENT_2026-07-01_SYSTEM_ORCHESTRATOR_VALIDATION.md`

### What Qwen Will Do
1. Read Gemini's output (handoff + gap analysis)
2. Validate each gap against actual codebase
3. Check if spec files exist (3 expected to be missing)
4. Audit mock data vs. real data transitions
5. Produce a research synthesis report

### Key Questions Qwen Must Answer
- [ ] Do referenced spec files (`settlement_manager_spec.rb`, `system_state_spec.rb`, `strategic_planner_spec.rb`) exist?
- [ ] Is `ResourceAllocator` dual-constructor claim correct? (confirm both signatures in code)
- [ ] Does `resource_allocator_spec.rb` test old single-settlement pattern? (will it break after merge?)
- [ ] Are transport capacities really hardcoded in `LogisticsCoordinator`? (quote the values)
- [ ] What would a real 2+ settlement integration test verify? (define scope)
- [ ] Can `Financial::Transaction` ledger entries be created for resource transfers? (check Financial module)

### Deliverable
📄 **Synthesis Report** saved to:  
```
/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
2026-07-01-RESEARCH-SYSTEM-ORCHESTRATOR-VALIDATION.md
```

**Includes:**
- Validation results for all 9 gaps
- Proposed task structure (count + dependencies)
- Risk assessment (nil failures, mock transitions, test coverage)
- Sprint feasibility estimate

---

## PHASE 3: CLAUDE REVIEW DECISION

**Status**: Pending Qwen's synthesis report  
**Checklist**: `CLAUDE_FINAL_REVIEW_CHECKLIST_2026-07-01`

### What Claude Will Review
1. **Scope**: Are all 9 gaps addressed or explicitly deferred?
2. **Risk**: Nil failures, test coverage, circular dependencies?
3. **Quality**: Task files have clear objectives, prerequisites, spec-creation steps?
4. **Architecture**: Proper layer separation, no scope creep per task?
5. **Tests**: Existing specs that will break documented? Baseline confirmed passing?
6. **Feasibility**: Realistic for sprint scope? Any "unknown unknowns"?

### Approval Decision
- ✅ **APPROVE** → Tasks ready for activation
- ⚠️ **CONDITIONAL APPROVE** → Minor fixes needed, re-submit after changes
- ❌ **REJECT** → Major blockers, loop back to Qwen for rework

---

## HANDOFF FILES (Ready to Use)

### For Qwen
📄 **Research Assignment**: `QWEN_RESEARCH_ASSIGNMENT_2026-07-01_SYSTEM_ORCHESTRATOR_VALIDATION.md`
- Contains: Full audit checklist, gap validation steps, report format
- Action: Copy into conversation with Qwen, he executes research

### For Claude
📄 **Review Checklist**: `CLAUDE_FINAL_REVIEW_CHECKLIST_2026-07-01`
- Contains: 7-point approval checklist, decision tree, sign-off template
- Action: After Qwen reports, paste his synthesis + use checklist to approve/reject

### Context Documents (For Both)
- Gemini's session handoff: `2026-06-30-1156-GEMINI-SESSION_HANDOFF_SUMMARY.md`
- Gap analysis: `2026-06-30-GAP-ANALYSIS-PROPOSED-TASKS.md`
- Research audit: `2026-06-30-RESEARCH-SYSTEM-ORCHESTRATOR-AUDIT.md`

---

## TASK BREAKDOWN RECOMMENDATION

Based on Gemini's gap analysis, expect ~6-7 final tasks (vs. 4 proposed):

| # | Task | Priority | Status |
|---|------|----------|--------|
| 1 | ResourceAllocator dual-constructor fix | HIGH | Existing ✅ |
| 2 | SettlementManager real data integration | HIGH | Existing ✅ (needs spec-creation step) |
| 3 | SystemState DB persistence | HIGH | Existing ✅ |
| 4 | StrategicPlanner extraction | HIGH | Existing ✅ |
| 5 | **NEW: Multi-Settlement Integration Test** | **CRITICAL** | **Gap #1** |
| 6 | **NEW: Complete Spec Coverage** (PriorityArbitrator + LogisticsCoordinator) | **HIGH** | **Gaps #4, #5** |
| 7 | Financial Integration (deferred?) | MEDIUM | Gap #6 |
| 8 | Wormhole Route Integration (deferred?) | MEDIUM | Gap #7 |
| 9 | EconomicForecaster Wiring (deferred?) | LOW | Gap #8 |

---

## WORKFLOW STEP-BY-STEP

### Step 1: Prepare Qwen (NOW)
```
1. Copy QWEN_RESEARCH_ASSIGNMENT_2026-07-01_*.md to conversation
2. Ensure he reads mandatory files first (GUARDRAILS, REVIEW_AGENT_GUIDE, Gemini's work)
3. Let him produce synthesis report (1-2 hours typical)
4. Get git-staged report back + paste output
```

### Step 2: Claude Reviews (AFTER Qwen)
```
1. Read Qwen's synthesis report (full text pasted)
2. Check each item in CLAUDE_FINAL_REVIEW_CHECKLIST_2026-07-01
3. Make approval decision (APPROVE / CONDITIONAL / REJECT)
4. If ✅ APPROVE: proceed to Step 3
5. If ⚠️ CONDITIONAL: document changes, send back to Qwen or Gemini
6. If ❌ REJECT: document blockers, await rework
```

### Step 3: Task Activation (IF APPROVED)
```
1. Create final task files based on Qwen's recommendations
2. Name: 2026-07-01-[PRIORITY]-[CATEGORY]-[DESCRIPTION].md
3. YAML header: status: backlog
4. Move to: projects/galaxy_game/tasks/backlog/
5. Git add + commit: "Add SystemOrchestrator task set (7 tasks, approved 2026-07-01)"
6. Update status.md: move to "In Progress" section
7. Ready for implementation agents to pick up
```

---

## SUCCESS CRITERIA (FOR THIS WORKFLOW)

- [ ] Qwen produces research report with all 9 gaps explicitly addressed
- [ ] Claude approves task structure (or provides clear rework scope)
- [ ] All new task files include "create spec" as step 1
- [ ] Dependency ordering is explicit in each task file
- [ ] Final task count and sprint estimate documented
- [ ] No implementation changes — research, review, and planning only
- [ ] Status.md updated with workflow completion notes

---

## RISKS & MITIGATIONS

| Risk | Mitigation |
|------|-----------|
| Qwen finds new gaps not in Gemini's analysis | Have Claude review findings; defer unknown gaps to next sprint |
| Spec files are harder to write than expected | Break into separate shorter tasks; use existing specs as templates |
| Task dependencies create blocking chain | Build dependency graph explicitly; identify parallelizable work |
| ResourceAllocator refactor breaks > 3 specs | Add migration/compatibility layer task; test thoroughly before cleanup |
| Multi-settlement test is too ambitious | Reduce to single "happy path" test; advanced scenarios in future sprint |

---

## NEXT ACTIONS

### Immediate (Now)
- [ ] Dispatch Qwen with research assignment (copy handoff into conversation)
- [ ] Ensure he has access to all reference documents
- [ ] Set expectation: produces synthesis report, does NOT implement

### After Qwen Reports
- [ ] Get synthesis report (git-staged + pasted output)
- [ ] Claude reviews using checklist (30 min review)
- [ ] Make approval decision (APPROVE / CONDITIONAL / REJECT)

### If Approved
- [ ] Finalize task files from Qwen's recommendations
- [ ] Move to `active/` folder
- [ ] Update status.md
- [ ] Hand off to implementation agents

---

**Created**: 2026-07-01  
**Process**: Multi-agent review (Qwen research → Claude approval → Task activation)  
**Workflow Maintained In**: `/memories/session/system_orchestrator_review_workflow.md`

