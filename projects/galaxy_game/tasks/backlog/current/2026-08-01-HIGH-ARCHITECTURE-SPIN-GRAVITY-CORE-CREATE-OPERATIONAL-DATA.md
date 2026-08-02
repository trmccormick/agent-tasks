---
status: backlog
priority: HIGH
type: architecture
system_domain: Settlement Infrastructure
mvp_alignment: SETTLEMENT_PHASE_4
local_worker_safe: true
created: 2026-08-01
last_updated: 2026-08-01
relates_to:
  - 2026-08-01-HIGH-DESIGN-SPIN-GRAVITY-CORE-ARCHITECTURE.md (completed — design specs embedded)
  - 2026-08-01-HIGH-ARCHITECTURE-SPIN-GRAVITY-CORE-VALIDATION.md (validation task — BLOCKED by missing file)
  - spin_gravity_core_mk1_bp.json (blueprint already exists)
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-01-HIGH-ARCHITECTURE-SPIN-GRAVITY-CORE-CREATE-OPERATIONAL-DATA.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-01-HIGH-ARCHITECTURE-SPIN-GRAVITY-CORE-CREATE-OPERATIONAL-DATA.md \
         projects/galaxy_game/tasks/active/2026-08-01-HIGH-ARCHITECTURE-SPIN-GRAVITY-CORE-CREATE-OPERATIONAL-DATA.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed
  Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-01-HIGH-ARCHITECTURE-SPIN-GRAVITY-CORE-CREATE-OPERATIONAL-DATA.md"
  Only ONE result should exist (in active/). Paste this output before committing.

READ FIRST (after Step 0): Task file contains full JSON spec to copy.
  Complete operational_data JSON embedded below (extracted from design task).
  Copy exactly as-is to: /Users/tam0013/Documents/git/galaxyGame/data/json-data/operational_data/units/infrastructure/gravity_systems/spin_gravity_core_mk1_data.json

CRITICAL: After file creation, validation task will be re-run.
  Validation task path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/active/2026-08-01-HIGH-ARCHITECTURE-SPIN-GRAVITY-CORE-VALIDATION.md
  That agent will re-run all 4 parts (now that file exists).
```

---

# TASK: Create Spin-Gravity Core Mk1 Operational Data File
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-08-01

---

## Context

Validation task (2026-08-01-HIGH-ARCHITECTURE-SPIN-GRAVITY-CORE-VALIDATION.md) discovered that the operational_data file for spin-gravity core does **not exist** in the codebase, even though the complete spec was embedded in the design task.

**Current state:**
- ✅ Blueprint file exists: `blueprints/units/infrastructure/spin_gravity_core_mk1_bp.json`
- ❌ Operational data file missing: `operational_data/units/infrastructure/gravity_systems/spin_gravity_core_mk1_data.json`
- 🔴 Validation blocked — Part 2 cannot proceed without this file

**Goal:** Create the missing operational_data file using the complete spec from the design task.

---

## Prerequisites — READ FIRST

1. **Design Task** (contains complete JSON reference): `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/completed/2026-08/2026-08-01-HIGH-DESIGN-SPIN-GRAVITY-CORE-ARCHITECTURE.md`
   - Section: "Existing Blueprint Data (COMPLETE REFERENCE FOR WEB AGENT)"
   - Sub-section: "spin_gravity_core_mk1_data.json (Current Full Spec)"
   - Copy the entire JSON block (lines starting after "```json")

2. **Target Directory Structure:**
   ```
   /Users/tam0013/Documents/git/galaxyGame/data/json-data/operational_data/
   └── units/
       └── infrastructure/
           └── gravity_systems/
               └── spin_gravity_core_mk1_data.json  ← Create here
   ```

3. **Validation Context:** After this file is created, the validation task will be re-run to verify all 4 parts pass.

---

## Critical Information

### Architecture Gotchas

⚠️ **GOTCHA 1: File Path Must Match Blueprint**
- ❌ Wrong: Create file at `units/production/gravity_systems/` (copy/paste error)
- ✅ Right: Create at `units/infrastructure/gravity_systems/` (mirrors blueprint location exactly)
- Why: Unit loader expects paired files in same directory structure

⚠️ **GOTCHA 2: JSON Must Be Valid**
- ❌ Wrong: Paste JSON and assume it's correct
- ✅ Right: Validate JSON syntax after creation (use `jq` or Rails console)
- Why: Malformed JSON breaks unit loading

⚠️ **GOTCHA 3: Do Not Modify Design Spec**
- ❌ Wrong: Edit values to "improve" them based on game balance
- ✅ Right: Copy spec exactly as embedded (design phase is complete)
- Why: Any changes require design task update (separate decision cycle)

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before doing anything, create and post a **synthesis report** in chat.

**Synthesis Report Template:**
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Create Spin-Gravity Core Mk1 Operational Data File
**Status**: backlog → active
**Date**: 2026-08-01

### What I'm About to Do
1. Read design task and locate JSON spec for spin_gravity_core_mk1_data.json
2. Extract complete JSON block from design task reference section
3. Create directory path if needed: operational_data/units/infrastructure/gravity_systems/
4. Write JSON file to: spin_gravity_core_mk1_data.json
5. Validate JSON syntax (use jq or Rails)
6. Commit file to git

### Files I'll Create
| File | Path | Status |
|---|---|---|
| spin_gravity_core_mk1_data.json | data/json-data/operational_data/units/infrastructure/gravity_systems/ | [not started] |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv
- ✅ YAML status updated from backlog → active
- ✅ Read design task (located JSON spec)
- ✅ Verified target directory structure
- ✅ Understand pairing requirement (must match blueprint location)

### Expected Outcomes
- File created at exact path: /Users/tam0013/Documents/git/galaxyGame/data/json-data/operational_data/units/infrastructure/gravity_systems/spin_gravity_core_mk1_data.json
- JSON passes syntax validation (jq parse check)
- File committed to git
- Ready for validation task to re-run Part 2

### Critical Gotchas I Will Avoid
- ❌ Wrong path (production instead of infrastructure) — instead ✅ mirror blueprint location exactly
- ❌ Copy only part of JSON — instead ✅ copy entire block as-is
- ❌ Edit values — instead ✅ paste spec unchanged
- ❌ Skip syntax check — instead ✅ validate with jq before commit

---

**SYNTHESIS COMPLETE.** Ready to proceed with file creation.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

The spin-gravity core design is complete (all 5 architectural decisions finalized). The blueprint file exists, but the operational_data file was never created in the codebase. 

**Impact:**
- Validation task blocked on Part 2 (cannot check operational_data fields)
- Settlement cannot load or spawn unit without paired operational_data file
- Blocks final validation before implementation fixes phase

**Goal:** Create the missing file using the complete spec from design task.

---

## Files Involved

### Primary File — you will create this
| File | Path | Purpose |
|---|---|---|
| `spin_gravity_core_mk1_data.json` | `data/json-data/operational_data/units/infrastructure/gravity_systems/` | Runtime specs: gravity performance, pod config, bearing systems, safety, power, crew health |

### Reference Files — read for JSON spec (do NOT edit)
| File | Location | Why You Need It |
|---|---|---|
| Design task (completed) | `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/completed/2026-08/2026-08-01-HIGH-DESIGN-SPIN-GRAVITY-CORE-ARCHITECTURE.md` | Contains complete JSON spec in "Existing Blueprint Data" section |

### Related Files — do NOT touch
| File | Why |
|---|---|
| `blueprints/units/infrastructure/spin_gravity_core_mk1_bp.json` | Already exists; operational_data must pair with this |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/galaxy_game/tasks/backlog/current/2026-08-01-HIGH-ARCHITECTURE-SPIN-GRAVITY-CORE-CREATE-OPERATIONAL-DATA.md \
       projects/galaxy_game/tasks/active/2026-08-01-HIGH-ARCHITECTURE-SPIN-GRAVITY-CORE-CREATE-OPERATIONAL-DATA.md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-01-HIGH-ARCHITECTURE-SPIN-GRAVITY-CORE-CREATE-OPERATIONAL-DATA.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

---

### Step 1 — Read Design Task and Extract JSON Spec

Open: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/completed/2026-08/2026-08-01-HIGH-DESIGN-SPIN-GRAVITY-CORE-ARCHITECTURE.md`

Navigate to section: **"Existing Blueprint Data (COMPLETE REFERENCE FOR WEB AGENT)"**

Locate sub-section: **"spin_gravity_core_mk1_data.json (Current Full Spec)"**

Copy the entire JSON block starting with:
```json
{
  "id": "spin_gravity_core_mk1",
  "name": "Spin-Gravity Core Mk I Operational Data",
  ...
}
```

**Paste the JSON into a text editor.** Do NOT modify it. Verify it starts with `{` and ends with `}`.

---

### Step 2 — Create Directory Structure (If Needed)

Check if the target directory exists:

```bash
ls -la /Users/tam0013/Documents/git/galaxyGame/data/json-data/operational_data/units/infrastructure/gravity_systems/
```

If directory does NOT exist:
```bash
mkdir -p /Users/tam0013/Documents/git/galaxyGame/data/json-data/operational_data/units/infrastructure/gravity_systems/
```

If directory EXISTS, proceed to Step 3.

---

### Step 3 — Create the File

**File path (exact):**
```
/Users/tam0013/Documents/git/galaxyGame/data/json-data/operational_data/units/infrastructure/gravity_systems/spin_gravity_core_mk1_data.json
```

**Content:** Paste the JSON from Step 1 (unchanged).

**Verification:** Check that file was created:
```bash
ls -la /Users/tam0013/Documents/git/galaxyGame/data/json-data/operational_data/units/infrastructure/gravity_systems/spin_gravity_core_mk1_data.json
```

Expected: File exists and is readable.

---

### Step 4 — Validate JSON Syntax

```bash
cd /Users/tam0013/Documents/git/galaxyGame && jq . data/json-data/operational_data/units/infrastructure/gravity_systems/spin_gravity_core_mk1_data.json
```

Expected output: JSON structure printed cleanly (no errors).

If jq is not installed:
```bash
docker exec -T web ruby -e 'require "json"; puts JSON.parse(File.read("/home/galaxy_game/data/json-data/operational_data/units/infrastructure/gravity_systems/spin_gravity_core_mk1_data.json")).pretty_inspect'
```

Expected: No parse errors.

---

### Step 5 — Verify File Pairing

Both files must now exist:

```bash
ls -la /Users/tam0013/Documents/git/galaxyGame/data/json-data/blueprints/units/infrastructure/spin_gravity_core_mk1_bp.json
ls -la /Users/tam0013/Documents/git/galaxyGame/data/json-data/operational_data/units/infrastructure/gravity_systems/spin_gravity_core_mk1_data.json
```

Expected: Both files exist.

---

### Step 6 — Commit to Git

```bash
cd /Users/tam0013/Documents/git/galaxyGame
git add data/json-data/operational_data/units/infrastructure/gravity_systems/spin_gravity_core_mk1_data.json
git commit -m "feat: Add spin_gravity_core_mk1_data.json — operational specs (gravity perf, pod config, bearing systems, safety, power, crew health)"
git push
```

---

### Step 7 — Complete Validation Task (After This)

After this file is committed, the validation task needs to be re-run:

**Path to validation task:** `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/active/2026-08-01-HIGH-ARCHITECTURE-SPIN-GRAVITY-CORE-VALIDATION.md`

**What happens next:**
- Validation agent will re-run all 4 parts (now that file exists)
- Part 2 will now validate all 60+ operational_data fields
- Complete validation report will be generated

Do NOT re-run validation yourself. Just commit this file and move task to completed.

---

## Acceptance Criteria
- [ ] File created at exact path: `operational_data/units/infrastructure/gravity_systems/spin_gravity_core_mk1_data.json`
- [ ] JSON passes syntax validation (jq parses cleanly)
- [ ] File contains exactly 1 object (not array, not multiple objects)
- [ ] File committed to git
- [ ] File pairs with existing blueprint (same naming pattern)
- [ ] No modifications to design spec values

---

## Stop Conditions — escalate to user immediately if:
- JSON fails syntax validation (malformed)
- Directory structure is non-standard or can't be created
- File creation fails (permissions issue)
- User provides conflicting instructions about file content

---

## Dependencies
**Blocked by**: 2026-08-01-HIGH-DESIGN-SPIN-GRAVITY-CORE-ARCHITECTURE.md (✅ completed — spec available)
**Blocks**: 2026-08-01-HIGH-ARCHITECTURE-SPIN-GRAVITY-CORE-VALIDATION.md (Part 2 validation can proceed after this)
**Related tasks**: None currently active

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Git commit**: [commit hash]

### What was created
- File: `data/json-data/operational_data/units/infrastructure/gravity_systems/spin_gravity_core_mk1_data.json`
- Size: [bytes]
- Fields: [count]
- Syntax validation: ✅ passed

### Verification
- File pairing with blueprint: ✅ confirmed
- JSON structure: ✅ valid
- Git commit: ✅ pushed

### Follow-up task
**Status:** Validation task is ready to re-run Part 2
**Path:** /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/active/2026-08-01-HIGH-ARCHITECTURE-SPIN-GRAVITY-CORE-VALIDATION.md

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: File created at operational_data/units/infrastructure/gravity_systems/ | JSON syntax valid | Git committed | Validation task ready to re-run Part 2
