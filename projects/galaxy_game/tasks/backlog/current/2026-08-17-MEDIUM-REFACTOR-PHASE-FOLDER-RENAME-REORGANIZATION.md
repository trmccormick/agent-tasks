---
status: backlog
priority: MEDIUM
type: refactor
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
created: 2026-08-17
---

# Task: Phase Folder Rename & Reorganization — Phases 9–13 Canonical Structure

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-17-MEDIUM-REFACTOR-PHASE-FOLDER-RENAME-REORGANIZATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-17-MEDIUM-REFACTOR-PHASE-FOLDER-RENAME-REORGANIZATION.md \
         projects/galaxy_game/tasks/active/2026-08-17-MEDIUM-REFACTOR-PHASE-FOLDER-RENAME-REORGANIZATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-17-MEDIUM-REFACTOR-PHASE-FOLDER-RENAME-REORGANIZATION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.
```

---

## Context

`phase09-sol-expansion/` has become a flat dump for all of Phases 9–13's work with no internal separation by target phase. Additionally, `phase10+/`, `phase13+/`, `phase14+/`, `phase15+/`, and `phase16+/` use non-canonical naming that doesn't match the PHASE_STRUCTURE.md table.

**Canonical authority**: PHASE_STRUCTURE.md's "Current Backlog State — Parallel Execution Model" table only. GALAXY-GAME-PHASE-ALIGNMENT.md is deprecated (June-dated, superseded) and must NOT be used as a co-equal reference.

**This is a file reorganization task only.** No code changes, no implementation. All work is git mv operations within the agent-tasks repo.

---

## Prerequisites — READ FIRST

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/docs/new_agent/TASK_TEMPLATE.md` (EXECUTOR Role section)
2. **Phase Structure**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/PHASE_STRUCTURE.md` — **canonical authority** for phase definitions, folder names, and parallel execution model
3. **Deprecated Reference (do NOT use as authoritative)**: `/Users/tam0013/Documents/git/galaxyGame/docs/planning/GALAXY-GAME-PHASE-ALIGNMENT.md` — June-dated document superseded by PHASE_STRUCTURE.md's "Current Backlog State — Parallel Execution Model" table. Contains conflicting folder names (e.g., `phase9+` vs `phase09-mars/`). If referenced for historical context only, note it is deprecated.
4. **This Task File**: Everything below

---

## Architecture Gotchas

⚠️ **GOTCHA 1: This is a planning/reorganization task, not implementation**
- ❌ Wrong: Run RSpec, modify codebase files, touch galaxy_game/ app code
- ✅ Right: Only git mv operations within agent-tasks repo task folders
- Why: This task organizes planning artifacts; it has zero impact on the game codebase

⚠️ **GOTCHA 2: Ambiguous items need human judgment — do not guess**
- ❌ Wrong: Pick a folder for ambiguous files without flagging them
- ✅ Right: List ambiguous items in your synthesis report and wait for direction
- Why: Moving a task to the wrong phase folder creates confusion that compounds over time

⚠️ **GOTCHA 3: Duplicate resolution — keep one version, mark others superseded**
- ❌ Wrong: Move both copies to the same folder (creates two identical files)
- ✅ Right: Keep the higher-priority or more-complete version; update the other's YAML status to `superseded`
- Why: Two identical task files in the same folder wastes space and creates confusion

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands, create and post a **synthesis report** in chat.

```markdown
## STATUS SYNTHESIS REPORT

**Task**: Phase Folder Rename & Reorganization — Phases 9–13
**Status**: backlog → active
**Date**: 2026-08-17

### What I'm About to Do
Reorganize task folders from non-canonical names (phase09-sol-expansion/, phase10+/, etc.) to canonical names matching PHASE_STRUCTURE.md. This involves: creating 4 new folders, redistributing ~17 files from phase09-sol-expansion/ to appropriate targets, renaming 2 existing folders, deleting 4 empty folders, resolving 4 duplicate pairs, standardizing 1 non-standard filename.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| PHASE_STRUCTURE.md | Canonical phase definitions and folder names | Read |
| backlog/phase09-sol-expansion/* (17 files) | Source files to redistribute | Inventory |
| backlog/phase10+/* (1 file) | Source file to move | Inventory |
| backlog/phase13+/* (1 file) | Source file to move | Inventory |
| backlog/phase14+/* (6 files) | Source files to keep + rename folder | Inventory |
| backlog/phase15+/* (3 files) | Source files to keep + rename folder | Inventory |
| backlog/phase16+/* (1 file) | Duplicate — delete folder | Inventory |

### Prerequisites Completed
- ✅ Read PHASE_STRUCTURE.md canonical definitions
- ✅ Inventory all files in source folders
- ✅ Identify duplicates across folders
- ✅ Flag ambiguous items requiring human judgment
- ✅ Understand this is planning-only (no code changes)

### Expected Outcomes
- 4 new canonical folders created: phase09-mars/, phase10-venus/, phase11-logistics/, phase12-optional-branches/
- 2 existing folders renamed: phase14+ → phase14-eden-expansion/, phase15+ → phase15-snap-crisis/
- ~17 files redistributed from phase09-sol-expansion/ to appropriate target folders
- 4 empty folders deleted: phase09-sol-expansion/, phase10+/, phase13+/, phase16+/
- 4 duplicate pairs resolved (keep one, mark other superseded)
- 1 non-standard filename standardized (add date prefix)
- All ambiguous items flagged for human review

### Critical Gotchas I Will Avoid
- ❌ Running RSpec or touching galaxy_game/ app code — this is planning-only
- ❌ Guessing on ambiguous file placements — will flag all 8 ambiguous items
- ❌ Moving both copies of duplicate pairs — will keep one, mark other superseded
- ❌ Deleting phase16+/ without verifying its only content is a duplicate

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 0.
```

---

## Problem Statement

Task folders for Phases 9–13 use non-canonical naming and are not separated by target phase:
- `phase09-sol-expansion/` contains 17 files spanning Mars, Venus, logistics, terraforming, and AI Manager domains
- `phase10+/`, `phase13+/` use shorthand naming that doesn't match PHASE_STRUCTURE.md
- `phase14+/`, `phase15+/` are close but need descriptive suffixes
- `phase16+/` contains only a duplicate file and should be eliminated
- 4 pairs of duplicate task files exist across folders

---

## Files Involved

### Source Folders (read-only inventory — will be emptied/deleted)
| Folder | File Count | Action |
|--------|-----------|--------|
| `backlog/phase09-sol-expansion/` | 17 | Redistribute contents, then delete folder |
| `backlog/phase10+/` | 1 | Move file to phase10-venus/, then delete folder |
| `backlog/phase13+/` | 1 | Move file to phase13-psyche/, then delete folder |
| `backlog/phase14+/` | 6 | Rename folder, keep contents |
| `backlog/phase15+/` | 3 | Rename folder, keep contents |
| `backlog/phase16+/` | 1 | Delete entire folder (duplicate content) |

### Target Folders (create or rename)
| New Path | Purpose | Source |
|----------|---------|--------|
| `backlog/phase09-mars/` | Mars orbital + surface + terraforming | Created from phase09-sol-expansion/ redistribution |
| `backlog/phase10-venus/` | Venus cloud cities + atmospheric ops | Created from phase10+/ content |
| `backlog/phase11-logistics/` | Multi-world cycler logistics | Created (may receive files from phase09-sol-expansion/) |
| `backlog/phase12-optional-branches/` | Ceres/Titan optional paths | Created (may receive files from phase09-sol-expansion/) |
| `backlog/phase13-psyche/` | Psyche mining + coordinated terraforming | Created from phase13+/ content |
| `backlog/phase14-eden-expansion/` | AI operational independence + Eden expansion | Renamed from phase14+/ |
| `backlog/phase15-snap-crisis/` | Unplanned Eden expansion + Snap crisis | Renamed from phase15+/ |

### Duplicate Pairs to Resolve
| Pair | Files | Action |
|------|-------|--------|
| 1 | RAW-RESOURCE-EXTRACTION-PRICING.md (Apr vs Jun in phase09-sol-expansion/) | Keep one, mark other superseded |
| 2 | TERRAFORMING-MANAGER-METHOD-SHADOWING.md (HIGH vs MEDIUM in phase09-sol-expansion/) | Keep HIGH, mark MEDIUM superseded |
| 3 | TERRAFORMING-MANAGER-DATA-DRIVEN.md (Mar vs Jun in phase14+/) | Compare content; keep one, mark other superseded |
| 4 | WORMHOLE-EXPANSION-SERVICE-AWS-CONSTRUCTION.md (phase15+/ vs phase16+/) | Delete phase16+ copy entirely |

### Non-Standard Filename to Fix
| File | Issue | Fix |
|------|-------|-----|
| `refactor_terraforming_manager_identify_available_resources.md` (in phase14+/) | No date prefix, lowercase | Rename to `2026-06-21-MEDIUM-REFACTOR-TERRAFORMING-MANAGER-IDENTIFY-AVAILABLE-RESOURCES.md` |

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
# From inside agent-tasks repo root:
git mv projects/galaxy_game/tasks/backlog/current/2026-08-17-MEDIUM-REFACTOR-PHASE-FOLDER-RENAME-REORGANIZATION.md \
       projects/galaxy_game/tasks/active/2026-08-17-MEDIUM-REFACTOR-PHASE-FOLDER-RENAME-REORGANIZATION.md
```

Then open the moved file and change: `status: backlog` → `status: active`

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-17-MEDIUM-REFACTOR-PHASE-FOLDER-RENAME-REORGANIZATION.md"
```

Paste the output in chat. Expected: exactly one result at the active/ path.

### Step 1 — Create Target Folders

```bash
cd /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog
mkdir -p phase09-mars phase10-venus phase11-logistics phase12-optional-branches phase13-psyche
```

### Step 2 — Rename Existing Folders

```bash
cd /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog
mv phase14+ phase14-eden-expansion
mv phase15+ phase15-snap-crisis
```

### Step 3 — Standardize Non-Standard Filename

```bash
cd /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/phase14-eden-expansion
mv refactor_terraforming_manager_identify_available_resources.md \
   2026-06-21-MEDIUM-REFACTOR-TERRAFORMING-MANAGER-IDENTIFY-AVAILABLE-RESOURCES.md
```

### Step 4 — Resolve Duplicates (Before Moving Files)

For each duplicate pair, read both files and determine which to keep:

**Pair 1**: RAW-RESOURCE-EXTRACTION-PRICING.md (Apr vs Jun in phase09-sol-expansion/)
- Read both files' content
- If same topic: keep one, update other's YAML status to `superseded`
- If different scope: may need separate destinations

**Pair 2**: TERRAFORMING-MANAGER-METHOD-SHADOWING.md (HIGH vs MEDIUM)
- Keep HIGH priority version
- Update MEDIUM version's YAML status to `superseded`

**Pair 3**: TERRAFORMING-MANAGER-DATA-DRIVEN.md (Mar vs Jun in phase14+/)
- Read both files' content
- If same topic: keep one, update other's YAML status to `superseded`

**Pair 4**: WORMHOLE-EXPANSION-SERVICE-AWS-CONSTRUCTION.md (phase15+ vs phase16+)
- Delete the phase16+ copy entirely (identical content)

### Step 5 — Redistribute phase09-sol-expansion/ Contents

Move files from `phase09-sol-expansion/` to appropriate target folders:

**→ phase09-mars/** (Mars foothold, surface, terraforming):
```bash
git mv phase09-sol-expansion/2026-04-12-MEDIUM-ARCHITECTURE-POPULATION-MANAGEMENT-CONCERN.md phase09-mars/
git mv phase09-sol-expansion/2026-06-22-HIGH-FEATURE-DIGITAL-TWIN-SIMULATION-SCHEMA.md phase09-mars/
git mv phase09-sol-expansion/2026-07-15-MEDIUM-REFACTOR-SUPERMARS-SETTLEMENT-SERVICE-REMOVAL.md phase09-mars/
```

**→ phase10-venus/** (Venus cloud cities):
```bash
# FOOTHOLD-ESTABLISHMENT-SERVICE.md is ambiguous — flag for human judgment
# If confirmed Venus: git mv phase09-sol-expansion/2026-06-07-MEDIUM-FEATURE-FOOTHOLD-ESTABLISHMENT-SERVICE.md phase10-venus/
```

**→ phase11-logistics/** (Multi-world cycler logistics):
```bash
git mv phase09-sol-expansion/2026-08-16-LOW-SPEC-DISTRIBUTE-CONSORTIUM-PROFITS.md phase11-logistics/
# RAW-RESOURCE-EXTRACTION-PRICING.md (Apr version) is ambiguous — flag for human judgment
```

**→ phase12-optional-branches/** (Ceres/Titan optional paths):
```bash
# ASTEROID-INTEGRITY-GATE.md is ambiguous — flag for human judgment
```

**→ phase13-psyche/** (Psyche mining + coordinated terraforming):
```bash
git mv phase09-sol-expansion/2026-07-05-LOW-RESEARCH-TERRAFORMING-ATMOSPHERIC-GAP-ANALYSIS.md phase13-psyche/
```

**→ phase14-eden-expansion/** (AI operational independence + Eden):
```bash
git mv phase09-sol-expansion/2026-05-28-LOW-ARCHITECTURE-WORLDHOUSE-STATE-SCHEMA.md phase14-eden-expansion/
git mv phase09-sol-expansion/2026-05-29-HIGH-ARCHITECTURE-MISSION-PROFILE-RECOMMENDATION-ENGINE-V2.md phase14-eden-expansion/
git mv phase09-sol-expansion/2026-08-03-HIGH-BUGFIX-TERRAFORMING-MANAGER-DEFAULT-PARAMS.md phase14-eden-expansion/
git mv phase09-sol-expansion/2026-08-03-HIGH-BUGFIX-TERRAFORMING-MANAGER-METHOD-SHADOWING.md phase14-eden-expansion/
git mv phase09-sol-expansion/2026-08-03-MEDIUM-REFACTOR-TERRAFORMING-MANAGER-HARDCODED-TARGETS.md phase14-eden-expansion/
# MEDIUM-BUGFIX-TERRAFORMING-MANAGER-METHOD-SHADOWING.md — mark superseded (duplicate of HIGH version)
```

**→ phase08-shipyards/** (craft design):
```bash
# SCOUT-SHIP-CRAFT-DESIGN.md is ambiguous — suggest phase08-shipyards/ but flag for human judgment
```

### Step 6 — Move Standalone Files from Shorthand Folders

**Ambiguous items — do NOT execute these moves without human direction:**

```bash
# phase10+/ → phase10-venus/
# git mv phase10+/2026-07-27-MEDIUM-REFACTOR-SIM-EVALUATOR.md phase10-venus/
# SIM-EVALUATOR.md is ambiguous — AI_MANAGER domain, no world specificity. Could validate any phase's simulation.

# phase13+/ → phase13-psyche/
# git mv phase13+/2026-07-27-MEDIUM-REFACTOR-SYSTEM-ARCHITECT.md phase13-psyche/
# SYSTEM-ARCHITECT.md is ambiguous — AI_MANAGER domain, could be general infrastructure or Eden-specific.
```

### Step 7 — Delete Empty Folders

```bash
cd /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog
rmdir phase09-sol-expansion phase10+ phase13+ phase16+
```

Verify each is empty before rmdir (should fail if any files remain):
```bash
ls phase09-sol-expansion/  # should show only .DS_Store or be empty
ls phase10+/              # should show only .DS_Store or be empty
ls phase13+/              # should show only .DS_Store or be empty
ls phase16+/              # should show only .DS_Store or be empty
```

### Step 8 — Verify Final State

```bash
cd /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog
echo "=== Canonical folders ==="
ls -d phase05-luna-calibration phase06-lava-tube-base phase07-depot-building phase08-shipyards phase09-mars phase10-venus phase11-logistics phase12-optional-branches phase13-psyche phase14-eden-expansion phase15-snap-crisis 2>&1

echo "=== No legacy folders remain ==="
ls -d phase09-sol-expansion phase10+ phase13+ phase16+ 2>&1 || echo "All legacy folders deleted ✅"

echo "=== File counts per canonical folder ==="
for d in phase*/; do echo "$d: $(ls -1 $d/*.md 2>/dev/null | wc -l) files"; done
```

### Step 9 — Commit

```bash
cd /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog
git add -A
git commit -m "refactor: rename and reorganize phase folders to canonical structure

- Create canonical folders: phase09-mars, phase10-venus, phase11-logistics, phase12-optional-branches, phase13-psyche
- Rename: phase14+ → phase14-eden-expansion, phase15+ → phase15-snap-crisis
- Redistribute 17 files from phase09-sol-expansion/ to appropriate target folders
- Resolve 4 duplicate pairs (keep one version per pair, mark others superseded)
- Standardize non-standard filename (add date prefix)
- Delete empty legacy folders: phase09-sol-expansion, phase10+, phase13+, phase16+
- Flag 8 ambiguous items for human review before final placement"
```

---

## Acceptance Criteria
- [ ] All 7 canonical folders exist with correct names matching PHASE_STRUCTURE.md
- [ ] No legacy folders remain (phase09-sol-expansion, phase10+, phase13+, phase16+)
- [ ] All files from phase09-sol-expansion/ redistributed to appropriate target folders
- [ ] 4 duplicate pairs resolved (one version kept, others marked superseded)
- [ ] Non-standard filename standardized with date prefix
- [ ] File counts per folder match expected distribution
- [ ] No galaxy_game/ app code touched — planning-only task
- [ ] All ambiguous items flagged in completion report for human review

---

## Stop Conditions — escalate to user immediately if:
- Any source folder contains files that cannot be classified by content (not just ambiguous, but truly unclassifiable)
- A duplicate pair has genuinely identical content where neither version is clearly superior
- git mv fails due to path issues or permission problems
- Any file in phase14+/ or phase15+/ has content that doesn't match the folder's canonical purpose

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "[type]: [brief description]"
git push
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-08-17-MEDIUM-REFACTOR-PHASE-FOLDER-RENAME-REORGANIZATION.md \
       projects/galaxy_game/tasks/completed/2026-08/2026-08-17-MEDIUM-REFACTOR-PHASE-FOLDER-RENAME-REORGANIZATION.md
git commit -m "chore: move phase folder reorganization task to completed/"
```

---

## Dependencies
**Blocked by**: none
**Blocks**: None — this is a standalone reorganization that unblocks future task filing clarity
**Related tasks**: PHASE_STRUCTURE.md (canonical definitions)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: 
**Completion date**: 
**Final state verification**: [paste Step 8 output]

### What was changed
- [List all folder renames, file moves, deletions]

### Ambiguous items flagged for human review
| # | File | Current Location | Suggested Target | Why Ambiguous |
|---|------|-----------------|------------------|---------------|
| 1 | | | | |
| 2 | | | | |
| ... | | | | |

### Duplicates resolved
| Pair | Kept Version | Superseded Version | Reason |
|------|-------------|-------------------|--------|
| 1 | | | |
| 2 | | | |
| 3 | | | |
| 4 | | | |

### Lessons learned
[What worked, what didn't, what future reorganization tasks should know]
