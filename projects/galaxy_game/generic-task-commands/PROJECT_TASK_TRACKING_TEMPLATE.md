# Galaxy Game — Project Task Tracking Template  
**Template Version**: 1.0 (June 9, 2026)  
**Purpose**: Standardized structure for planning agents to track Luna MVP progress and backlog triage sessions  
**Location**: `/docs/new_agent/projects/galaxy_game/PROJECT_TASK_TRACKING_TEMPLATE.md`

---

## 📊 Session Start Checklist (MANDATORY)

### 1. Establish Current Baseline
```bash
# Check last updated status:
cat ~/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/status.md | head -30

# Verify RSpec baseline before any work:
echo "=== AI Manager Suite ===" && \
docker-compose -f docker-compose.dev.yml exec web bundle exec rspec spec/services/ai_manager --format progress 2>&1 | tail -5

echo "=== Full Suite Overnight Log (if exists) ===" && \
cat ~/phase3_full_suite.log 2>/dev/null | grep -E "(examples?|failures?)" || echo "No overnight log found — run full suite if needed"
```

**Record in session notes:**
- [ ] RSpec baseline: ___ examples, ___ failures (flaky?), ___ pending
- [ ] Branch: main / feature branch name
- [ ] Last milestone completed: _________________________

---

### 2. Review Active Task Queue
```bash
# Check what's currently in active/backlog folders:
echo "=== ACTIVE TASKS ===" && \
ls -1 ~/Documents/git/agent-tasks/projects/galaxy_game/tasks/active/*.md 2>/dev/null || echo "(none)"

echo "" && echo "=== BACKLOG (Luna-focused, 2026-06/) ===" && \
ls -1 ~/Documents/git/agent-tacts/projects/galaxy_game/tasks/backlog/2026-06/*.md | wc -l && \
ls -1 ~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-06/*.md

echo "" && echo "=== PHASE 5+ BACKLOG ===" && \
ls -1 ~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase5+/[0-9]*.md 2>/dev/null | wc -l || echo "(none)"

echo "" && echo "=== PHASE 6+ BACKLOG (if folder exists) ===" && \
ls -1 ~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase6+/[0-9]*.md 2>/dev/null | wc -l || echo "(none)"
```

**Record in session notes:**
- [ ] Active tasks: ___ (list filenames)
- [ ] Luna backlog items: ___ total
- [ ] Phase 5+ backlog items: ___ total  
- [ ] Phase 6+ backlog items: ___ total

---

### 3. Check for Blockers or Pending Decisions
```bash
# Look for "Hold" status tasks in status.md:
grep -A2 "Active Tasks" ~/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/status.md | grep -i "hold\|blocked\|pending decision" || echo "(no blockers found)"

# Check if any task files reference missing dependencies:
echo "=== Checking for blocked tasks ===" && \
grep -l "BLOCKED_BY\|DEPENDS_ON" ~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/**/*.md 2>/dev/null || echo "(no explicit blockers in backlog)"
```

**Record in session notes:**
- [ ] Blockers: _________________________ (or "none")
- [ ] Pending human decisions: _________________________

---

## 🎯 Luna MVP Phase Tracking Matrix

| Phase | Service/Feature | Status | Task File Location | Gate Dependency | Last Updated |
|-------|----------------|--------|-------------------|-----------------|--------------|
| 1 | `Settlements::CostAnalyzer` → AI Manager | ✅ Complete | `/completed/cd4d6800/` | — | 2026-06-05 |
| 2 | `Logistics::ManifestGenerator` → AI Manager | ✅ Complete | `/completed/21b10ef0/` | Phase 1 complete | 2026-06-06 |
| 3 | `ShortageDetector + ImportRequestGenerator` → AI Manager | ✅ Complete | `/completed/32b31f54/` | Phases 1-2 complete | 2026-06-08 |
| 4 | Consumption-aware ordering + precursor phase awareness | 🔄 Backlog | `/backlog/2026-06/[CHECK HERE]` | Phase 3 complete | — |

**How to update this table:**
1. Check `status.md` for completed phases (look under "Luna Phase Queue")
2. Cross-reference with task files in `/completed/` folder using commit hashes from status.md
3. Mark backlog items as 🔄 Backlog until handoff is generated and sent to Implementation Agent

---

## 🔍 Triage Session Workflow Template

### When Starting a New Triage Session (e.g., reviewing April 2026 backlog):

**Step 1: Read Required Context First**
```bash
# Always start here — understand current project state before reviewing legacy tasks:
cat ~/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/status.md | head -50  
cat ~/Documents/git/galaxyGame/docs/new_agent/README.md | grep -A20 "Session Roles"

# Check what's already in Phase folders (avoid duplicating work):
ls -la ~/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/phase5+/ 2>/dev/null || echo "(folder doesn't exist yet)"
```

**Step 2: Identify Target Files to Review**
- **Folder pattern**: `docs/agent/archive/backlog_[MONTH]_2026/*.md` or specific files provided by user
- **Session objective**: "Extract actionable tasks from [LEGACY SOURCE], categorize into Phase folders, archive originals with extraction notes"

**Step 3: For Each Legacy Task File Reviewed:**
```markdown
### [TASK FILE NAME].md — Review Notes

**Original Location**: `docs/agent/archive/backlog_april_2026/[FILENAME].md`  
**Task Type**: architecture design / feature implementation / refactor / bug fix (circle one)  
**Current Status Check**: 
- Is this already implemented? → Run: `grep -r "class [ServiceName]" ~/Documents/git/galaxyGame/app/ 2>/dev/null | head -3`
- Does code exist but need AI Manager integration? → Check existing service file location

**Decision Made:**
- ✅ **Extract as new task**: Create in `/backlog/[PHASE]/[TASK_ID].md` with updated YAML header and current date prefix
  - Example: `2026-06-09-HIGH-FEATURE-[SHORT-TITLE].md` (Phase 5+) or `phase5+/2026-06-09-[PRIORITY]-[TITLE].md`
  
- ❌ **Archive as superseded**: Move to `/deprecated/`, add header note explaining why:
  ```markdown
  <!-- ARCHIVED: [DATE] — Superseded by implementation in commit [HASH], or task split into smaller units -->
  <!-- Original location: docs/agent/archive/backlog_april_2026/[FILENAME].md -->
  ```

- 🔄 **Hold for human decision**: Add to status.md "Active Tasks" section with question/context needed
```

**Step 4: Commit Task Files After Each Session**
```bash
cd ~/Documents/git/agent-tasks

# Stage new task files created during session:
git add 'projects/galaxy_game/tasks/backlog/[PHASE]/[NEW_TASK_FILES].md'

# Archive deprecated originals (if any):
git add 'projects/galaxy_game/deprecated/*.md'  # or specific file paths

# Commit with clear message summarizing extraction work:
git commit -m "feat: Extract [X] actionable tasks from [SOURCE FOLDER] backlog review

- Task 1 title ([PHASE]) — brief description of what it does
- Task 2 title ([PHASE]) — brief description  
- Archived original files to deprecated/ with header notes"

# Push to remote (human approval required before push):
git push origin main
```

---

## 📝 Session End Checklist (MANDATORY)

### Update status.md Before Closing Session

**1. Update "Last Updated" timestamp at top of file:**
```markdown
**Last Updated**: 2026-06-[DATE] ([SESSION TYPE] — [BRIEF SUMMARY])
```

**2. Add completed work summary under appropriate date header:**
```markdown
### 2026-06-[DATE] Session ([Session Type])
- ✅ **[TASK TITLE]** — Brief description of what was done
  - Commit: [HASH] (if applicable)
  - Files created/modified: [LIST KEY FILES]
  - Result: [SPEC RESULTS OR OUTCOME SUMMARY]

**Total Tasks Created Tonight**: X new tasks across Phase Y+ folders  
**Originals Archived to deprecated/**: Z files with extraction notes
```

**3. Update "Active Tasks" section if any blockers emerged:**
```markdown
| File | Agent | Status |
|---|---|---|
| `[TASK_FILE].md` | Hold / In Progress / Blocked | [Brief note on what's needed] |
```

**4. Update Luna Phase Queue table if phases completed:**
- Mark phase as ✅ Complete with commit hash and date
- Add next phase to backlog if ready for implementation

---

## 🚦 Task File Naming Convention (Standardized)

### Format: `[DATE]-[PRIORITY]-[TYPE]-[SHORT_TITLE].md`

**Examples:**
```markdown
2026-06-09-HIGH-FEATURE-WORMHOLE-TOPOLOGY-INTEGRATION.md  
2026-06-08-MEDIUM-FEATURE-STANDARDIZE-ORBITAL-STRUCTURE-DEPLOYMENT.md  
2026-06-03-HIGH-BUGFIX-SHORTAGE-DETECTOR-JSONB-STRING-KEY.md
```

**Priority Levels:**
- `CRITICAL` — Blocks all other work (production bug, security issue)
- `HIGH` — Luna MVP core feature or blocker for next phase  
- `MEDIUM` — Enhancement or Phase 5+ prep work
- `LOW` — Nice-to-have, documentation cleanup

**Task Types:**
- `FEATURE` — New functionality implementation
- `BUGFIX` — Fixing broken behavior (regression)
- `REFACTOR` — Code improvement without changing external behavior  
- `ARCHITECTURE` — Design document or service blueprint creation
- `INTEGRATION` — Wiring existing services into AI Manager

---

## 📂 Folder Structure Reference

```
agent-tasks/projects/galaxy_game/
├── tasks/
│   ├── backlog/
│   │   ├── 2026-06/          # Luna-focused active backlog (Phase 1-4)
│   │   ├── phase5+/           # Orbital infrastructure prep, LDC operations  
│   │   └── phase9+/           # Act 3/4 work (NOT MVP focus — archive here if found in legacy review)
│   ├── active/                # Tasks currently being implemented by Executor agents
│   └── completed/             # Archived tasks with commit hashes as folder names
├── deprecated/                # Original backlog files moved during triage sessions  
├── research/                  # Domain expert analysis, architectural decisions (read-only for planning)
└── handoffs/                  # Session-specific assignment messages (if needed — usually conversational only)

galaxyGame/docs/new_agent/projects/galaxy_game/
├── status.md                  # Living project baseline document (UPDATE EVERY SESSION)  
├── PROJECT_TASK_TRACKING_TEMPLATE.md  # This file — your workflow guide
└── generic-task-commands/     # Reusable command templates for common operations
```

---

## 🧠 Quick Reference: Common Planning Agent Commands

### Check if a service already exists before creating task files:
```bash
# Search for existing implementation:
grep -r "class [ServiceName]" ~/Documents/git/galaxyGame/app/services/ 2>/dev/null | head -3  
docker-compose exec web grep -r "[ServiceName].rb" app/services/ 2>/dev/null || echo "(not found)"

# Check if AI Manager integration exists:
grep -A5 "execute_[feature_name]" ~/Documents/git/galaxyGame/app/services/ai_manager/strategy_selector.rb | head -10  
```

### Verify RSpec baseline before starting work:
```bash
# Targeted suite (fast, 2-3 min):
docker-compose exec web bundle exec rspec spec/services/[SPEC_FILE] --format progress 2>&1 | tail -5  

# Full AI Manager suite (~8 min):  
docker-compose exec web bundle exec rspec spec/services/ai_manager --format progress 2>&1 | tail -5

# Overnight full suite (run before session if baseline unclear — ~40-60 min, async mode recommended)
```

### Find task file dependencies:
```bash
# Search for references to a service across codebase:
grep -r "[ServiceName]" ~/Documents/git/galaxyGame/app/ 2>/dev/null | wc -l  
docker-compose exec web grep -rn "Logistics::[ClassName]" app/services/ spec/services/ 2>/dev/null | head -10

# Check if factory exists for a model:
grep -A3 "[ModelName].factory" ~/Documents/git/galaxyGame/spec/factories/*.rb || echo "(no factory found)"  
```

---

## 🔄 When to Use This Template

**Use this template when:**
- ✅ Starting a new planning session (review backlog, generate handoffs)
- ✅ Conducting legacy task triage sessions (April 2026 archive cleanup)
- ✅ Updating status.md at end of any implementation session  
- ✅ Creating new task files from research notes or architectural decisions

**Do NOT use this template when:**
- ❌ You're acting as an Implementation Agent executing a handoff — follow the HANDOFF_TEMPLATE.md instead
- ❌ Running ad-hoc maintenance work (RSpec failures, quick fixes) — route to available executor first per agent workflow rules

---

## 📚 Related Documentation Links

- **Main README**: `/docs/new_agent/README.md` — Generic multi-project workflow guide  
- **Session Strategist Guide**: `/rules/SESSION_STRATEGIST.md` — Detailed role definition and triage patterns
- **Handoff Templates**: 
  - Simple: `/SIMPLE_HANDOFF_TEMPLATE.md` (straightforward tasks)
  - Advanced: `/HANDOFF_TEMPLATE.md` (complex multi-file work requiring synthesis reports)  
- **Guardrails & Rules**: `/rules/GUARDRAILS.md`, `/rules/DECISIONS.md` — Locked architectural decisions

---

**Template Last Updated**: June 9, 2026  
**Next Review Date**: After Luna Phase 4 completion or when workflow patterns evolve significantly
