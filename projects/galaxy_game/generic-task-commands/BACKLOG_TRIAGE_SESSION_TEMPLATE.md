# Backlog Triage Session — Standard Operating Procedure  
**Template Version**: 2.0 (June 14, 2026)  
**Role**: Task Reviewer / Strategist Agent  
**Session Type**: Legacy Backlog Audit & Task Extraction

## What Changed in v2.0:
- ✅ Added duplicate detection step with grep examples before creating new tasks
- ✅ Enhanced decision matrix to handle partial overlap scenarios
- ✅ Updated TRIAGE_REGISTRY.md count formulas for accurate tracking
- ✅ Fixed session summary location (research/ not backlog/)
- ✅ Added Phase 5 calibration warning in task creation section
- ✅ Included git commit message examples for different scenarios
- ✅ Updated status.md entry format with concrete example
- ✅ Corrected current phase folder state counts from June 12 data

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
| **Already covered by existing backlog tasks** (duplicate of active/Phase 5+ task) | DUPLICATE — ALREADY TRACKED 🔁 | Archive to deprecated/ with header note pointing to existing task file location. No new work needed unless gap analysis shows missing scope not covered by existing task. |
| **Duplicate concept exists in Phase folders** (same topic, different date/priority) | DUPLICATE — CONCEPT EXISTS 🔁 | Read existing task(s) to compare scope. If legacy task adds NEW aspects → extract those gaps as separate task with clear differentiation. If fully redundant → archive without creating new file.

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
- `RESEARCH`: Investigation/audit task requiring analysis before implementation can begin

#### File Location Rules:
```bash
# Phase 5+ Tasks (Luna settlement + single-system infrastructure):
~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase5+/2026-MM-DD-PRIORITY-TYPE-DESCRIPTION.md

# Phase 6+ Tasks (future scalability enhancements, multi-system operations):  
~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase6+/2026-MM-DD-PRIORITY-TYPE-DESCRIPTION.md
```

⚠️ **CRITICAL PHASE 5 CALIBRATION NOTE**: Phase 5 is for simulation calibration and observation, NOT feature development. Only create phase5+ tasks if they are prerequisites needed BEFORE the Luna simulation can run (e.g., AI Manager training pipeline audit). Do not add new features to phase5+ — those belong in phase6+.

**CRITICAL**: Create `phase6+/` and `phase9+/` folders if they don't exist:
```bash
mkdir -p ~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/{phase6+,phase9+}  
```

#### Duplicate Detection (MANDATORY BEFORE CREATING NEW TASK):
**CRITICAL**: Check for existing duplicate tasks across all Phase folders before creating new file:
```bash
# Search for similar concepts using keywords from legacy task title/description
grep -ri "[KEYWORD1].*[KEYWORD2]" ~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase*/*.md  

# Example: Checking for procedural map generation duplicates:
grep -rni "PROCEDURAL.*MAP.*GENERATION\|TERRAIN.*QUALITY" ~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase*/*.md
```
**If matches found:**
1. Read existing task file(s) to compare scope and implementation details
2. Determine if legacy task covers NEW aspects not addressed by existing tasks
3. If fully redundant → archive without creating new file (mark as DUPLICATE)
4. If partial overlap → extract only the gaps with clear differentiation in title/description
5. Document decision in archive header explaining why duplicate was/wasn't created

#### Git Commit Message Examples:
```bash
# Single task file created:
git add projects/galaxy_game/tasks/backlog/phase5+/2026-06-XX-PRIORITY-TYPE-DESCRIPTION.md
git commit -m "feat: Add Phase 5+ task for [brief description] — blocks [dependency]"

# Multiple related tasks from single legacy file review:
git add projects/galaxy_game/tasks/backlog/phase*/*.md
git commit -m "feat: Extract actionable tasks from [legacy filename] backlog review

- Task A (Phase X) — brief scope summary
- Task B (Phase Y) — blocked by prerequisite Z"

# Archive cleanup only (no new tasks created):
cd ~/Documents/git/galaxyGame
git add docs/new_agent/projects/galaxy_game/research/BACKLOG_TRIAGE_*.md
git commit -m "docs: update April 2026 backlog triage session — [N] files reviewed, pattern analysis included"

# Push to agent-tasks repo (task file changes):
cd ~/Documents/git/agent-tasks
git push origin main
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

### Update Dashboard Counts Formula:
- **Legacy Remaining**: Count files in source backlog folder excluding deprecated/ subfolder
  ```bash
  ls ~/Documents/git/galaxyGame/docs/agent/archive/backlog_april_2026/*.md | grep -v deprecated | wc -l
  ```
- **Ready for Copilot**: Sum of all `.md` files across phase5+/, phase6+/, phase9+/ folders (excluding .DS_Store)
  ```bash
  find ~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase* -name "*.md" ! -path "*/deprecated/*" | wc -l
  ```
- **Completed & Cleaned**: Count archived files in deprecated/ subfolders with header notes added
  ```bash
  find ~/Documents/git/galaxyGame/docs/agent/archive/backlog_april_2026/deprecated -name "*.md" | wc -l
  ```

### Add to Active Sprint Pipeline:
- **Active Sprint Pipeline section**: Add new tasks created tonight with priority/domain/dependencies columns (use existing table format)
- **Notes & Context section**: Add "Recent Triage Session (June X, 2026)" entry summarizing work completed

### 2. Update status.md Project Status File  
**Location**: `docs/new_agent/projects/galaxy_game/status.md`  
**Sections to Modify:**

### Example status.md Entry Format:
```markdown
### 2026-06-[DATE] Session ([SESSION TYPE])
- ✅ **[N] legacy backlog files reviewed** from [SOURCE FOLDER]
  - Status breakdown: [X] OBSOLETE, [Y] DUPLICATE, [Z] GENUINE GAP → actionable work extracted
- 📝 **Task files created:**
  - `phase5+/2026-MM-DD-PRIORITY-TYPE-DESCRIPTION.md` — [brief scope summary]
  - `phase6+/...` (if applicable)
  - `phase9+/...` (if applicable)
- 🗂️ **Files archived to deprecated/:** [list filenames with header notes added]
- 🔍 **Pattern analysis:** [e.g., "~83% implementation rate across reviewed files"]
```

### Update Sections:
1. **Header date line**: Change from previous session date to current date + "(Session Type — Brief Description)"
2. **Completed section**: Add new subsection using format above with bullet points: tasks reviewed, files created/archived

### 3. Update MASTER_TRIAGE_GUIDE.md Version Number (If Applicable)  
**Location**: `docs/new_agent/projects/galaxy_game/tasks/backlog/MASTER_TRIAGE_GUIDE.md`  
**Sections to Modify:**
- **Version line at top**: Increment version if workflow guidance was updated during session

### 4. Create SESSION_SUMMARY_[DATE]_EVENING.md (Optional but Recommended)
**Location**: `docs/new_agent/projects/galaxy_game/research/BACKLOG_TRIAGE_[SOURCE]_SESSION.md`  
**Purpose**: Complete session record for future reference, includes:
- Executive summary with task counts and file locations
- Detailed breakdown of each legacy task reviewed + status found
- Corrections applied mid-session (if any naming/folder issues discovered)
- Next steps recommendations for future sessions
- Pattern analysis (implementation rate, common gaps identified)

---

## 🎯 Quality Checklist Before Ending Session

**Verify All Tasks Created Have:**
- ✅ Correct filename format: `YYYY-MM-DD-PRIORITY-TYPE-BRIEF-DESCRIPTION.md`  
- ✅ YAML header with full date (not just YYYY-MM), matching filename ID field
- ✅ Placed in correct Phase folder (`phase5+/` vs `phase6+/`) based on MVP alignment and dependencies
- ✅ Clear "In Scope" + "Out of Scope" sections defining boundaries explicitly  
- ✅ Dependencies section listing prerequisite tasks that must complete first
- ⚠️ **Phase 5 Calibration Check**: If placed in phase5+, verify it's a simulation calibration PREREQUISITE (not a feature). Phase 5 is observation, not development — only add tasks needed BEFORE Luna simulation can run.

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

**Current Phase Folder State**:
⚠️ **UPDATE THIS SECTION AT END OF EACH SESSION** — counts below are stale after June 12 session.

- `backlog/2026-06/` — empty (all tasks complete or moved to phase folders)
- `phase5+/` — 1 task (`AI Manager training pipeline audit`) 
- `phase6+/` — 7 tasks (gas processing, orbital deployment ×2 duplicates, dynamic position, multi-structure routing, pressurization TTR ×2, AI market calibration research)
- `phase9+/` — 6 tasks (wormhole easter egg + refactor, foothold establishment, resource coordination, wormhole topology integration, procedural map quality)

**Note**: Duplicate task files exist in phase6+ (`TTR-METRIC` appears twice). Future sessions should consolidate or remove duplicates.

**Recommended Next Steps After Tonight's Work:**
1. Continue April 2026 backlog triage — apply phase alignment table strictly
2. Focus on Luna simulation gaps only (Phase 5 calibration items)
3. Skip wormhole/multi-system work entirely — move to phase9+ without detailed review
4. Flag any processed_slag, GCC tax variable, or foundry prerequisite tasks as HIGH priority bug fixes
5. **Before creating new task**: Always run duplicate detection grep search across all Phase folders first

---

**END OF STANDARD OPERATING PROCEDURE TEMPLATE**  
*Copy this file to your session context and fill in the [BRACKETED PLACEHOLDERS] with tonight's specific targets.*
