# Reorganization Attempt 3 — Task Sorting Holding Area

**Date Created**: 2026-06-22  
**Last Updated**: 2026-06-22 (Session handoff)  
**Purpose**: Hold tasks from misplaced phase5+ folder for systematic review and proper reclassification  
**Status**: 13 tasks processed this session, 22 remaining awaiting review

---

## Context

During phase5+ audit (2026-06-22), we discovered that **phase5+ was being used as a general "everything Luna-related" bucket**, when it should contain **ONLY prerequisites needed to run Luna simulation testing**.

Per **DEVELOPMENT_ROADMAP.md** canonical principle:
> "Only create tasks that are prerequisites needed BEFORE the Luna simulation can run. Do NOT add new features to phase5+."

This folder holds all 46 originally misplaced tasks. Tasks are reviewed one at a time, codebase verified, and reclassified to proper destinations:
- **phase5**: Luna MVP prerequisites
- **phase6+**: Post-Luna infrastructure
- **phase9+**: Advanced systems
- **design/**: UI/rendering tasks
- **procedural_generation/**: Post-bootstrap system generation
- **deprecated/**: Verified resolved

---

## Progress This Session (2026-06-22)

### Processed (13 tasks) ✅
**Moved to phase5** (Luna MVP):
- 2026-06-21-HIGH-RESEARCH-HLT-PAYLOAD-CAPACITY-AND-MASS-DATA-GAPS.md
- 2026-02-11-HIGH-AI_MANAGER-SITE-SELECTION-ALGORITHM.md
- 2026-02-11-HIGH-FEATURE-AI-MANAGER-ESCALATION-DEPENDENCIES.md
- 2026-02-11-HIGH-FEATURE-AI-MANAGER-RESOURCE-ALLOCATION-ENGINE.md
- 2026-02-11-HIGH-FEATURE-AI-MANAGER-SERVICE-INTEGRATION.md
- 2026-02-15-HIGH-FEATURE-IMPLEMENT-SETTLEMENT-PATTERN-LOGIC.md
- 2026-03-27-HIGH-REFACTOR-ESCALATION-SERVICE-NO-MAGIC-ROBOT-DEPLOYMENT.md
- 2026-06-17-LUNA-SIMULATION-LOOP.md
- 2026-04-04-HIGH-BUGFIX-MATERIAL-PROCESSING-SERVICE-PVE-VOLATILES.md
- 2026-06-21-CRITICAL-FEATURE-LUNA-INITIAL-EQUIPMENT-PROVISIONING.md
- 2026-06-17-HIGH-FEATURE-LUNA-Bootstrap-Manufacturing-Loop.md
- 2026-05-27-HIGH-ARCHITECTURE-VERIFY-TASK-EXECUTION-ENGINE-TASKS-V2.md
- 2026-05-27-HIGH-REFACTOR-AI-MANAGER-USE-SETTLEMENT-DEPLOYMENT-SERVICE.md ⚠️ **CRITICAL BLOCKER**

**Moved to phase6+** (post-Luna):
- 2026-04-03-LOW-DATA-CO2-OXYGEN-PRODUCTION-UNIT-SCHEMA-AND-STOICHIOMETRY.md
- 2026-04-12-MEDIUM-ARCHITECTURE-POPULATION-MANAGEMENT-CONCERN.md

**Moved to design/** (UI/rendering):
- 2026-04-17-ADVANCED-CLAUDE-INVESTIGATE-TERRAIN-QUALITY.md

**Moved to procedural_generation/** (post-bootstrap):
- 2026-05-23-HIGH-BUG-FIX-UNIVERSE-REGISTRATION-JOB-SPATIAL-BOUNDARY-VALIDATION.md

**Moved to deprecated/** (resolved):
- 2026-04-01-MEDIUM-BUG-FIX-ITEM-REGOLITH-GEOSHERE.md

**Updated with findings** (held in reorganization_attempt_3 for later):
- 2026-04-04-MEDIUM-BUGFIX-MANUFACTURING-PIPELINE-E2E-THERMAL-EXTRACTION.md (stale)
- 2026-05-18-HIGH-FEATURE-ISRU-TRACK-A-RSPEC-SUITE.md (stale, needs research)
- 2026-05-18-MEDIUM-DATA-ISRU-OPERATIONAL-DATA-FIXTURES.md (stale, dependent)
- 2026-05-18-MEDIUM-REFACTOR-ISRU-UNIT-MANAGER-INTEGRATION.md (stale, part of cluster)

**Skipped for clarification**:
- 2026-04-04-MEDIUM-BUG-FIX-GAME-ADVANCE-BY-DAYS-NEGATIVE-GUARD.md (needs review)

### Remaining (22 tasks) 🔄
See **REORGANIZATION_AUDIT_CONTINUATION_HANDOFF.md** for complete list and next steps

---

## Key Findings

**CRITICAL BLOCKER IDENTIFIED:**
- `2026-05-27-HIGH-REFACTOR-AI-MANAGER-USE-SETTLEMENT-DEPLOYMENT-SERVICE.md`
  - All 3 direct `BaseSettlement.create!` calls still exist and bypass `SettlementDeploymentService`
  - `task_execution_engine_v2.rb:107` is actively used in the `rake luna_mission:execute` command
  - Needs immediate refactoring: use `establish_from_craft` instead of direct create!

**STALE WORK CLUSTERS:**
- **ISRU Track A (2026-05-18 trio)**: IsruTrackA service doesn't exist in codebase; needs recreation based on current TEU/PVE implementations
- **Manufacturing Pipeline E2E**: Task description references old line numbers; line numbers have drifted or code was refactored

**VERIFICATION COMPLETE:**
- ✅ TaskExecutionEngineV2 correctly reads from tasks_v2 library
- ✅ tasks_v2 directory populated with 100+ extracted task JSON files
- ✅ HLT payload research task ready for dispatch (blocking manifest validation)

---

## Next Session Instructions

**Continue audit with REORGANIZATION_AUDIT_CONTINUATION_HANDOFF.md:**

1. Process ONE task at a time
2. Read and verify codebase state
3. Determine correct destination
4. Move with relocation notes or hold for later
5. Do NOT batch operations

**Start with next task:** `2026-05-18-HIGH-ARCHITECTURE-ISRU-TRACK-A-SPEC-GAP-AUDIT.md` (needs research per conversation notes)
   - *Correct location*: Needs review for deduplication with `2026-02-11-CRITICAL-BUGFIX-ADMIN-MONITOR-TURBO-LOADING.md`
   - *Issue*: Both concern monitor/planetary canvas rendering; may need to merge scope

---

## Action Items

### For Each Task, Determine:
- [ ] **Is this real work?** Code artifacts exist? Or just proposal?
- [ ] **What phase does it belong in?** (phase0, phase6+, phase9+, design/, or post-MVP backlog)
- [ ] **Should it be converted to TASK_TEMPLATE?** (Some are already in template format)
- [ ] **Can it be deprecated?** (If design/proposal stage and no code progress)

### Prioritize Review:
1. **High priority**: planetary-view-phase1.md (check for duplicate with monitor task)
2. **Medium priority**: drone-bay and component-catalog (need phase6+ confirmation)
3. **Low priority**: assets, audit, investigation tasks (can be scheduled later)

---

## What phase5+ SHOULD Contain (Post-Cleanup)

**Phase 5+ Intent** (per DEVELOPMENT_ROADMAP.md):
- Luna settlement simulation bootstrap and testing prerequisites ONLY
- AI Manager decision logic, ISRU production validation
- Data-driven configuration (manifest, mission profiles)
- Atmospheric/environmental state tracking
- Monitor/visualization canvas rendering (foundational UI)
- **NOT**: Asset generation, component catalogs, design work, post-MVP features

**Current phase5+ status after this move**: 39 tasks (down from 46)
**Target**: ~25-30 tasks (only actual Luna MVP blockers)

---

## Completion Checklist

Once you review each task:
- [ ] Decide correct destination phase/folder
- [ ] Update task file with header explaining relocation + rationale
- [ ] Move task to correct folder
- [ ] Delete this folder when all tasks are reclassified

---

**Last Updated**: 2026-06-22  
**Status**: Awaiting review and reclassification
