---
status: backlog
priority: HIGH
type: architecture
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: false
---

# TASK: Verify TaskExecutionEngine Reads tasks_v2 Library

**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-05-27
**Last Updated**: 2026-05-27

---

## Local Worker Triage Report

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS
- **MVP Alignment**: VALID — AI Manager cannot execute blueprint-driven missions without correct tasks_v2 integration
- **MVP Impact Note**: If TaskExecutionEngine uses hardcoded logic instead of tasks_v2, AI Manager mission execution is broken for Luna settlement
- **Action Line**: READY FOR CLOUD HANDOFF after orbital_resupply_cycle extraction complete

---

## Agent Assignment

**Assigned To**: GPT-4.1 0x
**Why This Agent**: Audit and targeted fix, well-defined verification steps
**Supervision Level**: Watched carefully

---

## Context

`TaskExecutionEngine` is designed as a pure mission task runner that reads task definitions from `app/data/json-data/missions/tasks_v2/`. The engine initializes with a `mission_id`, loads a task list and manifest, and executes tasks in sequence. This task verifies the integration is working correctly and fixes any gaps.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules

---

## Problem Statement

**Current behavior unknown** — need to verify:
- Does `load_task_list` correctly read from `tasks_v2/`?
- Does `load_manifest` correctly parse mission JSON?
- Are task handlers implemented for core task types (deploy_unit, survey, ferry, excavate)?
- Are there any hardcoded task behaviors remaining after `orbital_resupply_cycle` extraction?

**Expected behavior**: Engine reads any mission JSON, executes tasks from `tasks_v2` library, passes outputs as inputs to next task, tracks progress.

---

## Files Involved

### Primary Files — audit then fix if needed
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/ai_manager/task_execution_engine.rb` | Pure task runner | `load_task_list`, `load_manifest`, task handlers |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/data/json-data/missions/tasks_v2/` | Task library — verify exists and has content |

---

## Implementation Steps

### Step 1 — Audit task library

```bash
ls galaxy_game/app/data/json-data/missions/tasks_v2/ | head -20
```

```bash
wc -l galaxy_game/app/services/ai_manager/task_execution_engine.rb
```

### Step 2 — Audit load methods and task handlers

```bash
grep -n "def load_task_list\|def load_manifest\|def execute\|def handle\|tasks_v2" galaxy_game/app/services/ai_manager/task_execution_engine.rb
```

### Step 3 — Synthesis Report

```
SYNTHESIS REPORT
tasks_v2 directory exists: YES/NO
tasks_v2 file count: N
load_task_list reads from tasks_v2: YES/NO/PARTIAL
load_manifest reads mission JSON: YES/NO/PARTIAL
Task handlers implemented: [list found]
Task handlers missing: [list gaps]
Hardcoded logic remaining after orbital_resupply_cycle removal: YES/NO
ISSUES: [describe any gaps]
PROPOSED FIX: [exact changes needed]
READY TO APPLY? — waiting for approval
```

### Step 4 — Fix gaps (after approval)

Fix any missing integration between engine and tasks_v2 library. Do not rewrite the engine — targeted fixes only.

### Step 5 — Verify

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/task_execution_engine_spec.rb 2>&1 | tail -20'
```

Expected: 0 failures.

---

## Acceptance Criteria
- [ ] tasks_v2 library exists and has content
- [ ] `load_task_list` correctly reads from tasks_v2
- [ ] `load_manifest` correctly parses mission JSON
- [ ] No hardcoded world/material logic remains in engine
- [ ] Engine spec passes — 0 failures

---

## Stop Conditions
- tasks_v2 directory empty or missing — stop, report
- Engine is >500 lines with extensive hardcoded logic — escalate before touching
- Fix requires new task handler implementations — scope separately

---

## Commit Instructions
```bash
git add galaxy_game/app/services/ai_manager/task_execution_engine.rb
git commit -m "arch: verify and fix TaskExecutionEngine tasks_v2 integration"
git push
```

---

## Dependencies
**Blocked by**: `2026-05-27-CRITICAL-REFACTOR-EXTRACT-ORBITAL-RESUPPLY-CYCLE.md`
**Blocks**: AI Manager mission execution, Sol validation test
**Related**: `2026-05-27-HIGH-FEATURE-ORBITAL-CONSTRUCTION-LOGISTICS-SERVICE.md`

---

## Completion Report
**Completed by**:
**Completion date**:
**Final test result**:

### What was changed
### Issues discovered
### Follow-up tasks needed
### Lessons learned

---

## Handoff Summary
HANDOFF SUMMARY: TaskExecutionEngine tasks_v2 integration verified | Engine world-neutral | Mission execution unblocked
