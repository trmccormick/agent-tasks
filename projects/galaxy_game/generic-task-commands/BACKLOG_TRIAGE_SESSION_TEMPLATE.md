# Backlog Triage Session — Standard Operating Procedure  
**Template Version**: 1.0 (June 9, 2026)  
**Role**: Task Reviewer / Strategist Agent  
**Session Type**: Legacy Backlog Audit & Task Extraction

---

## 🎯 Session Objective

Review legacy backlog files from archived folders (`docs/agent/archive/backlog_*`), compare against current codebase implementation status, and extract actionable task files into appropriate Phase folders (`backlog/phase5+/`, `backlock/phase6+/`). Archive originals to deprecated/ subfolder with header notes explaining extraction rationale.

---

## 📋 Pre-Session Checklist (MANDATORY)

### 1. Read Required Documentation First
```bash
# STEP 1: Read phase alignment reference — do this BEFORE reviewing any task files
# This is the authoritative guide for which phase folder any task belongs in
cat ~/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/research/LUNA-MVP-SIMULATION-DESIGN.md

# STEP 2: Understand current project state
cat ~/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/status.md | head -50  
cat ~/Documents/git/galaxyGame/docs/new_agent/README.md | head -100

# STEP 3: Check what's already in Phase folders (avoid duplicating work):
ls -la ~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase5+/
ls -la ~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase6+/ 2>/dev/null || echo "Phase 6+ folder doesn't exist yet"
ls -la ~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase9+/ 2>/dev/null || echo "Phase 9+ folder doesn't exist yet"

# STEP 4: Review current triage registry for context:
cat ~/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/TRIAGE_REGISTRY.md | head -80  
```

### Research Folder
Additional reference documents live at:
```
~/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/research/
```

Key files:
| File | Purpose |
|---|---|
| `LUNA-MVP-SIMULATION-DESIGN.md` | **PRIMARY REFERENCE** — Phase structure, acceptance criteria, game design intent |
| `BACKLOG_TRIAGE_ANALYSIS_JUNE_9.md` | June 9 triage findings — what was reviewed and decisions made |
| `BACKLOG_TRIAGE_ANALYSIS_PHASE_ALIGNMENT.md` | Phase alignment analysis from June 9 session |
| `LUNAR_GEOSPHERE_BASE.md` | Luna geology reference for ISRU-related tasks |
| `REORGANIZATION_COMPLETE_FOR_CLAUDE_REVIEW_JUNE_9.md` | Current task folder inventory as of June 9 |

### 2. Identify Target Backlog Folder & Files to Review
**Example Session Scenarios:**
- **April 2026 backlog triage**: `docs/agent/archive/backlog_april_2026/*.md` (~57 files)  
- **March 2026 backlog triage**: `docs/agent/archive/backlog_march_2026/*.md` (if exists)
- **Specific file review**: User provides exact path(s) to audit

**Tonight's Target Files:** [USER TO FILL IN — list specific files or folder pattern]  
Example: "Review all wormhole-related tasks from April 2026 backlog" → `docs/agent/archive/backlog_april_2026/*wormhole*.md`

---

## 📐 Phase Alignment Quick Reference

**Authoritative source:** `research/LUNA-MVP-SIMULATION-DESIGN.md` — read this first every session.

| Phase Folder | What Belongs Here | Gate Condition |
|---|---|---|
| `backlog/2026-06/` | Luna AI Manager mechanics only — consumption modeling, precursor phase gating, life support ordering, import request fixes | Active work — dispatch immediately when ready |
| `phase5+/` | Luna simulation calibration prep — skimmer processing, multi-source supply, tank farm architecture, inbound cargo awareness, CH4 arbitration, flight time modeling | After Phase 4 complete and simulation running |
| `phase6+/` | L1 Depot, LEO Depot, gas processing pipeline, depot operations, orbital structure deployment, return cargo optimization | After Luna simulation calibrated (Phase 5 acceptance criteria met) |
| `phase9+/` | Everything else — wormhole topology, multi-system expansion, Mars/Phobos/Deimos, outer Sol, Act 3/4 work | After L1 Shipyard operational |

**Classification rules when in doubt:**

```
Does this touch Luna AI Manager import/ordering logic directly?
  YES → active backlog/2026-06/

Does this require Luna simulation to be calibrated first?
  YES → phase5+/

Does this require L1 Depot or LEO Depot to exist?
  YES → phase6+/

Does this involve wormholes, other star systems, or multi-system operations?
  YES → phase9+/

Does this involve Mars, Phobos/Deimos, cycler construction, or tug program?
  YES → phase9+/
```

**Game Design Context (required for correct classification):**

- **LDC** is a non-profit development corporation and GCC mint — optimization goal is expansion not profit
- **GCC** is asset-backed cryptocurrency — Luna stockpiles are the hard asset backing
- **tasks_v2** is the AI pattern library — already built, AI Manager reads it to decide actions
- **Phase 5 is observation not features** — run simulation, watch market emergence and stockpile accumulation, generate tasks from what breaks
- **Luna must prove the fuel loop closes** before anything downstream gets built
- **AstroLift + LDC** co-own L1 Station, LEO Depot, L1 Shipyard — Zenith Orbital builds stations, Vector Hauling runs cyclers

---

## 🔄 Standard Workflow Pattern (Follow Exactly)

### Step 1: Read Legacy Task File
```bash
# Open the task file and read full contents:
cat ~/Documents/git/galaxyGame/docs/agent/archive/backlog_april_2026/[TASK_FILE_NAME].md | head -150  
```

**What to Look For:**
- **Task type**: architecture design, feature implementation, refactor, bug fix?
- **Priority level**: CRITICAL/HIGH/MEDIUM/LOW (from YAML header or task description)
- **System domain**: AI_MANAGER, ORBITAL_INFRASTRUCTURE, ISRU, LOGISTICS, etc.  
- **Implementation scope**: What files/services/models are mentioned as targets?

### Step 2: Audit Current Codebase Implementation Status
**Run these diagnostic commands to verify what's already implemented:**

#### For Architecture/Feature Tasks (Most Common):
```bash
# Check if target models exist and have expected associations:
find ~/Documents/git/galaxyGame/galaxy_game/app/models -name "*[TARGET_MODEL]*" | xargs ls 2>/dev/null  
cat ~/Documents/git/galaxyGame/galaxy_game/app/models/[PATH_TO_TARGET].rb | head -80

# Check if target services exist and have expected methods:
find ~/Documents/git/galaxyGame/galaxy_game/app/services -name "*[TARGET_SERVICE]*" | xargs ls 2>/dev/null  
grep -rn "def [EXPECTED_METHOD]" ~/Documents/git/galaxyGame/galaxy_game/app/services/ --include="*.rb" | head -10

# Check for related migrations (confirms database schema exists):
find ~/Documents/git/galaxyGame/galaxy_game/db/migrate* -name "*[TARGET]*" | xargs ls 2>/dev/null  
```

#### For Bug Fix Tasks:
```bash
# Search for the bug pattern or error mentioned in task description:
grep -rn "[BUG_PATTERN_OR_ERROR_MESSAGE]" ~/Documents/git/galaxyGame/galaxy_game/app/ --include="*.rb" | head -10  

# Check if fix already exists (look for related commits or code changes):  
git log --oneline --all -- "~/Documents/git/galaxyGame/galaxy_game/[TARGET_FILE_PATH]" 2>/dev/null | head -5
```

#### For Refactor Tasks:
```bash
# Verify current implementation pattern vs. desired pattern from task description:
cat ~/Documents/git/galaxyGame/galaxy_game/app/models/structures/orbital_structure.rb | grep -A 5 "after_create"  
grep -rn "[OLD_PATTERN]" ~/Documents/git/galaxyGame/galaxy_game/app/services/ --include="*.rb" | head -10
```

### Step 3: Determine Task Status & Action Required

**Decision Matrix:**

| Current State Found | Legacy Task Status | Action to Take |
|---------------------|-------------------|----------------|
| **Fully implemented + tested** (models, services, specs all exist and work) | OBSOLETE — SUPERSEDED BY IMPLEMENTATION ✅ | Archive to deprecated/ with header note explaining what was built. No new task needed unless gaps identified during audit. |
| **Partially implemented** (reference pattern exists but isolated/incomplete) | PARTIALLY IMPLEMENTED — NEEDS COMPLETION ⚠️ | Extract actionable Phase 5+ or Phase 6+ task file(s). Archive original with header note explaining extraction rationale + dependency chain. |
| **Not yet implemented** (task describes greenfield work, no related code exists) | ACTIONABLE — READY FOR EXTRACTION 📝 | Create new high-fidelity task file in appropriate Phase folder based on MVP alignment and dependencies. Archive original to deprecated/. |
| **Already covered by existing backlog tasks** (duplicate of active/Phase 5+ task) | DUPLICATE — ALREADY TRACKED 🔁 | Archive to deprecated/ with header note pointing to existing task file location. No new work needed. |

### Step 4: Create New Task File(s) If Needed

#### Naming Convention (MANDATORY):
```
YYYY-MM-DD-PRIORITY-TYPE-BRIEF-DESCRIPTION.md  
Example: 2026-06-09-HIGH-FEATURE-STANDARDIZE-ORBITAL-STRUCTURE-DEPLOYMENT.md
```

**Priority Levels:**
- **CRITICAL**: Blocks all other work, immediate showstopper (rare)
- **HIGH**: Phase 5+ prerequisite or core MVP feature  
- **MEDIUM**: Enhancement task, can be deferred to Phase 6+ if needed
- **LOW**: Nice-to-have optimization, documentation cleanup

**Type Values:**
- `FEATURE`: New functionality implementation  
- `REFACTOR`: Code restructuring without changing external behavior
- `BUGFIX`: Fixing broken or incorrect existing code  
- `ARCHITECTURE`: Design document creation (rare — most architecture tasks become feature/refactor work)
- `DOCUMENTATION`: Writing guides, READMEs, API docs

#### File Location Rules:
```bash
# Phase 5+ Tasks (Luna settlement + single-system infrastructure):
~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase5+/2026-MM-DD-PRIORITY-TYPE-DESCRIPTION.md

# Phase 6+ Tasks (future scalability enhancements, multi-system operations):  
~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase6+/2026-MM-DD-PRIORITY-TYPE-DESCRIPTION.md
```

**CRITICAL**: Create `phase6+` folder if it doesn't exist:
```bash
mkdir -p ~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase6+  
```

#### Task File Template Structure (Use This Format):
See existing Phase 5+/Phase 6+ tasks for examples. Key sections required:
1. YAML header with id, status, priority, type, created date, system_domain, mvp_alignment
2. Context & Strategic Purpose section explaining why this task matters  
3. Problem Statement (current state vs expected behavior)
4. Current State Analysis table showing what exists in codebase
5. Implementation Scope (In Scope + Out of Scope sections with clear boundaries)
6. Technical Requirements subsections for each implementation phase
7. Testing Requirements (unit, integration, system tests needed)  
8. Dependencies & Blockers section listing prerequisite tasks
9. Success Criteria & Acceptance Tests checklist

### Step 5: Archive Original Legacy File to Deprecated/ Folder

**Move file and add header note:**
```bash
# Move original task to deprecated subfolder:
mv ~/Documents/git/galaxyGame/docs/agent/archive/backlog_april_2026/[ORIGINAL_FILE].md \
   ~/Documents/git/galaxyGame/docs/agent/archive/backlog_april_2026/deprecated/

# Then edit the file and add archive header at top (before original content):
```

**Archive Header Template:**
```markdown
--- ARCHIVED: [STATUS] ---  
Original task requested [BRIEF DESCRIPTION]. **Core infrastructure already implemented between April-June 2026.** This file is preserved for historical reference only.

### What Was Implemented (Supersedes Original Task)
- ✅ Component A — fully live with RSpec coverage  
- ✅ Component B — integrated into Luna Phase X, commit hash: [HASH]  
- ⚠️ Component C — partially implemented, gap extracted as new task below  

### What Was Extracted as New Task(s) (Actionable Work Remaining)
📄 `docs/new_agent/projects/galaxy_game/tasks/backlog/phase5+/2026-MM-DD-PRIORITY-TYPE-DESCRIPTION.md`  
[Brief explanation of what the new task covers and why it's Phase 5+ vs Phase 6+]

### Implementation Evidence (For Reference)
See docs/new_agent/projects/galaxy_game/status.md — "Confirmed Architecture" section:
[Specific evidence from codebase audit showing implementation exists]
```

---

## 📊 Session Tracking Updates Required

After completing all file reviews, update these tracking documents:

### 1. Update TRIAGE_REGISTRY.md Dashboard Counts
**Location**: `docs/new_agent/projects/galaxy_game/tasks/backlog/TRIAGE_REGISTRY.md`  
**Sections to Modify:**
- **Triage Summary Dashboard table**: Adjust counts for Legacy Remaining, Ready for Copilot, Completed & Cleaned
- **Active Sprint Pipeline section**: Add new tasks created tonight with priority/domain/dependencies columns
- **Notes & Context section**: Add "Recent Triage Session (June X, 2026)" entry summarizing work completed

### 2. Update status.md Project Status File  
**Location**: `docs/new_agent/projects/galaxy_game/status.md`  
**Sections to Modify:**
- **Header date line**: Change from previous session date to current date + "(Evening Session — [Brief Description])"
- **Completed section**: Add new subsection for tonight's work with bullet points: tasks reviewed, files created/archived

### 3. Update MASTER_TRIAGE_GUIDE.md Version Number (If Applicable)  
**Location**: `docs/new_agent/projects/galaxy_game/tasks/backlog/MASTER_TRIAGE_GUIDE.md`  
**Sections to Modify:**
- **Version line at top**: Increment version if workflow guidance was updated during session

### 4. Create SESSION_SUMMARY_[DATE]_EVENING.md (Optional but Recommended)
**Location**: `docs/new_agent/projects/galaxy_game/tasks/backlog/SESSION_SUMMARY_YYYY-MM-DD_EVENING.md`  
**Purpose**: Complete session record for future reference, includes:
- Executive summary with task counts and file locations  
- Detailed breakdown of each legacy task reviewed + status found  
- Corrections applied mid-session (if any naming/folder issues discovered)  
- Next steps recommendations for future sessions

---

## 🎯 Quality Checklist Before Ending Session

**Verify All Tasks Created Have:**
- ✅ Correct filename format: `YYYY-MM-DD-PRIORITY-TYPE-BRIEF-DESCRIPTION.md`  
- ✅ YAML header with full date (not just YYYY-MM), matching filename ID field
- ✅ Placed in correct Phase folder (`phase5+/` vs `phase6+/`) based on MVP alignment and dependencies
- ✅ Clear "In Scope" + "Out of Scope" sections defining boundaries explicitly  
- ✅ Dependencies section listing prerequisite tasks that must complete first

**Verify All Archived Files Have:**
- ✅ Moved to appropriate deprecated/ subfolder (not just deleted)  
- ✅ Archive header added at top explaining status and extraction rationale  
- ✅ Links created pointing to new task file(s) if extracted actionable work exists

**Verify Tracking Documents Updated:**
- ✅ TRIAGE_REGISTRY.md dashboard counts reflect current state accurately  
- ✅ status.md has tonight's session entry with complete summary of work done  
- ✅ SESSION_SUMMARY_[DATE]_EVENING.md created (optional but recommended for complex sessions)

---

## 📝 Example Session Flow (From June 8, 2026 Evening Triage)

**Target**: Review April 2026 backlog items related to orbital infrastructure  
**Files Reviewed**: `2026-04-10-HIGH-ARCHITECTURE-ORBITAL-MARKET-SYSTEM.md`, `2026-04-12-HIGH-FEATURE-ORBITAL-STRUCTURE-LOCATION-DEPLOYMENT.md`

### Task #1 Review Process:
```bash
# Step 1 — Read legacy task file  
cat ~/Documents/git/galaxyGame/docs/agent/archive/backlog_april_2026/2026-04-10-HIGH-ARCHITECTURE-ORBITAL-MARKET-SYSTEM.md | head -150

# Step 2 — Audit current implementation  
cat ~/Documents/git/galaxyGame/galaxy_game/app/models/market/order.rb | head -80
grep -rn "def place_order\|def match_orders" ~/Documents/git/galaxyGame/galaxy_game/app/services/ --include="*.rb" | head -10

# Step 3 — Determine status: OBSOLETE (market infrastructure live) but gap identified  
# → Extract gas processing pipeline as Phase 5+ task  

# Step 4 — Create new task file with proper naming convention
create_file ~/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/phase5+/2026-06-08-HIGH-FEATURE-ORBITAL-GAS-PROCESSING-PIPELINE.md

# Step 5 — Archive original to deprecated/ with header note  
mv ~/Documents/git/galaxyGame/docs/agent/archive/backlog_april_2026/2026-04-10*.md \
   ~/Documents/git/galaxyGame/docs/agent/archive/backlog_april_2026/deprecated/

# Then add archive header explaining extraction rationale  
```

**Result**: 1 legacy task reviewed → 1 actionable Phase 5+ task created + original archived with documentation trail preserved.

---

## 🚨 Common Pitfalls to Avoid (Learned from June 8-9 Sessions)

### ❌ DON'T:
- Place Phase 6+ tasks in `phase5+/` folder — use correct phase folder per alignment table above
- Place wormhole/multi-system tasks anywhere except `phase9+/` — these are Act 3/4 work
- Place Mars/tug/cycler tasks in phase5+ or phase6+ — they belong in `phase9+/`
- Use incomplete date format (`2026-06`) instead of full YYYY-MM-DD (`2026-06-08`)
- Skip adding archive headers to deprecated files (future agents need context)
- Forget updating tracking documents at end of session
- Create phase5+ task files for features — Phase 5 is observation/calibration, tasks emerge from simulation runs

### ✅ DO:
- Read `research/LUNA-MVP-SIMULATION-DESIGN.md` before triaging anything
- Create `phase6+/` and `phase9+/` folders if they don't exist before moving tasks there
- Always use full date format in filenames AND YAML header ID fields
- Add comprehensive archive headers explaining extraction rationale + dependency chains
- Update TRIAGE_REGISTRY.md, status.md, and optionally create SESSION_SUMMARY file
- When unsure of phase, apply the classification rules in the Phase Alignment section above

---

## 🤖 Handoff Notes for Next Session Agent

**Git Status Check Required Before Starting:**
```bash
cd ~/Documents/git/agent-tasks  
git status --short projects/galaxy_game/tasks/backlog/phase5+/2026-*.md \
  projects/galaxy_game/tasks/backlog/phase6+/*.md \
  projects/galaxy_game/tasks/backlog/phase9+/*.md 2>/dev/null  

# If untracked files exist from previous session, commit them first before starting new work:
git add projects/galaxy_game/tasks/backlog/[NEW_FILES]  
git commit -m "feat: [Brief description of tasks added]"  
git push origin main
```

**Always read research folder at session start:**
```bash
ls -la ~/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/research/
# Read LUNA-MVP-SIMULATION-DESIGN.md before triaging anything
```

**Current Phase Folder State (as of June 9, 2026):**
- `backlog/2026-06/` — 4 tasks, Luna-focused, 2 ready to dispatch
- `phase5+/` — 2 tasks (orbital structure deployment, return cargo optimization)
- `phase6+/` — 3 tasks (gas processing pipeline, dynamic orbital position, multi-structure routing)
- `phase9+/` — 5 tasks (wormhole/multi-system Act 3/4 work — do not dispatch)

**Recommended Next Steps After Tonight's Work:**
1. Continue April 2026 backlog triage — apply phase alignment table strictly
2. Focus on Luna simulation gaps only (Phase 5 calibration items)
3. Skip wormhole/multi-system work entirely — move to phase9+ without detailed review
4. Flag any processed_slag, GCC tax variable, or foundry prerequisite tasks as HIGH priority bug fixes

---

**END OF STANDARD OPERATING PROCEDURE TEMPLATE**  
*Copy this file to your session context and fill in the [BRACKETED PLACEHOLDERS] with tonight's specific targets.*
