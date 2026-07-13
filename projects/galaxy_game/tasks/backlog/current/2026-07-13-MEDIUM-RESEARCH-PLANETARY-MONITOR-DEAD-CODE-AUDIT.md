---
status: backlog
priority: MEDIUM
type: research
system_domain: RENDERING | ADMIN_UI
mvp_alignment: PLANETARY_MONITORING_UI
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Research Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-07-13-MEDIUM-RESEARCH-PLANETARY-MONITOR-DEAD-CODE-AUDIT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/galaxyGame && git mv \
    docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-07-13-MEDIUM-RESEARCH-PLANETARY-MONITOR-DEAD-CODE-AUDIT.md \
    docs/new_agent/projects/galaxy_game/tasks/active/2026-07-13-MEDIUM-RESEARCH-PLANETARY-MONITOR-DEAD-CODE-AUDIT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of the find command before proceeding.

LIFECYCLE: backlog → active → completed

READ the task file for full research scope before starting work.
```

---

# TASK: Planetary Monitor Dead Code Audit

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: research
**Created**: 2026-07-13
**Last Updated**: 2026-07-13

---

## Local Worker Triage Report
- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A (research task)
- **MVP Alignment**: VALID — cleanup prevents confusion and bloat
- **MVP Impact Note**: No MVP impact — purely code hygiene
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

---

## Context

During the 2026-07-12 biome canvas rendering fix, several backup copies of `monitor.js` were discovered in `galaxy_game/app/javascript/admin/`. The most notable is `monitor_patched.js` (1591 lines), which contains sphere CRUD functions (`createSphere`, `editSphere`, `deleteSphere`) that are **not wired up** — no routes, no controller, no UI buttons call them.

This task is to audit all dead code in the planetary monitor and determine what can be safely removed vs. what might be needed later.

---

## Research Scope

### Files to Audit
| File | Lines | Known Status |
|---|---|---|
| `galaxy_game/app/javascript/admin/monitor.js` | ~1490 | Active — biome toggle fix applied 2026-07-13 |
| `galaxy_game/app/javascript/admin/monitor_patched.js` | 1591 | Backup — contains sphere CRUD, different layer naming (`water` vs `liquid`) |
| `galaxy_game/app/javascript/admin/monitor.js.new2.js` | ? | Unknown backup |
| `galaxy_game/app/javascript/admin/monitor.js.new3.js` | ? | Unknown backup |
| `galaxy_game/app/javascript/admin/monitor.js.new4.js` | ? | Unknown backup |
| `galaxy_game/app/javascript/admin/monitor.js.new5.js` | ? | Unknown backup |
| `galaxy_game/app/javascript/admin/monitor.js.new6.js` | ? | Unknown backup |
| `galaxy_game/app/javascript/admin/monitor.js.new7.js` | ? | Unknown backup |
| `galaxy_game/app/javascript/admin/monitor.js.new8.js` | ? | Unknown backup |
| `galaxy_game/app/javascript/admin/monitor.js.new9.js` | ? | Unknown backup |

### Questions to Answer

1. **Are any `.new*.js` files meaningfully different from `monitor_patched.js`?**
   - Check if they contain unique features not in either `monitor.js` or `monitor_patched.js`
   - If identical to one of the two, they're pure duplicates — safe to remove

2. **Is sphere CRUD needed?**
   - Check if any controller has a `SpheresController` or nested `spheres` routes
   - Check if any ERB template has buttons calling `AdminMonitor.createSphere()` etc.
   - Check if the `celestial_bodies` model has a `has_many :spheres` association
   - If none of the above → dead code, safe to remove

3. **Is there anything in `monitor_patched.js` worth keeping?**
   - Dynamic desert biome color (latitude-based) — nice-to-have but not critical
   - Sphere CRUD — see question 2
   - Everything else is formatting/whitespace differences from `monitor.js`

4. **Are there any other dead code patterns in monitor.js?**
   - Unused functions
   - Dead layer toggle options
   - Orphaned console.log debug statements

---

## Research Steps

### Step 0 — Move task file (MANDATORY)

```bash
cd /Users/tam0013/Documents/git/galaxyGame && git mv \
  docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-07-13-MEDIUM-RESEARCH-PLANETARY-MONITOR-DEAD-CODE-AUDIT.md \
  docs/new_agent/projects/galaxy_game/tasks/active/2026-07-13-MEDIUM-RESEARCH-PLANETARY-MONITOR-DEAD-CODE-AUDIT.md
```

Update YAML: `status: backlog` → `status: active`

### Step 1 — List all backup files

```bash
ls -la /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/javascript/admin/monitor*.js
wc -l /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/javascript/admin/monitor*.js
```

### Step 2 — Check for sphere infrastructure

```bash
# Routes
grep -rn "spheres" /Users/tam0013/Documents/git/galaxyGame/galaxy_game/config/routes.rb

# Controller
find /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/controllers -name "*sphere*"

# Model association
grep -rn "has_many :spheres\|belongs_to :sphere" /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/models/

# UI buttons
grep -rn "createSphere\|editSphere\|deleteSphere" /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/views/
```

### Step 3 — Diff all backup files against monitor_patched.js

```bash
for f in /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/javascript/admin/monitor.js.new*.js; do
  echo "=== $(basename $f) ==="
  diff "$f" /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/javascript/admin/monitor_patched.js | head -5
done
```

### Step 4 — Check for unused code in monitor.js

```bash
# Find functions defined but never called (excluding event handlers)
grep -n "^  function " /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/javascript/admin/monitor.js
```

Cross-reference each function name against the rest of the file to see if it's actually used.

---

## Deliverables

### Research Report (save as MD file)

```markdown
## DEAD CODE AUDIT REPORT

**Date**: 2026-07-13
**Auditor**: [agent name]

### Findings

#### 1. Backup Files
| File | Status | Action |
|---|---|---|
| monitor_patched.js | [identical to X / has unique features Y] | [remove / keep] |
| monitor.js.new2.js | [identical to X / has unique features Y] | [remove / keep] |
| ... | ... | ... |

#### 2. Sphere CRUD
- Routes: [found/not found]
- Controller: [exists/does not exist]
- Model association: [has_many :spheres/no association]
- UI buttons: [found in X ERB / none]
- Verdict: [dead code — remove / potentially needed — keep]

#### 3. monitor.js Dead Code
| Function | Used? | Action |
|---|---|---|
| functionName | [yes/no] | [keep/remove] |

### Recommended Actions
1. [Action item with file path]
2. [Action item]
3. ...

### Risk Assessment
- Removing X would break: [nothing / feature Y]
- Keeping X costs: [minimal bloat / confusion risk]
```

---

## Acceptance Criteria
- [ ] All `.new*.js` files identified as duplicates or unique
- [ ] Sphere CRUD infrastructure fully audited (routes, controller, model, UI)
- [ ] Research report saved with clear recommended actions
- [ ] No code changes made — research only, cleanup is a separate task

---

## Stop Conditions — escalate to user immediately if:
- Any `.new*.js` file contains unique features not in `monitor_patched.js` or `monitor.js`
- Sphere CRUD has partial infrastructure (e.g., routes exist but no controller)
- Research reveals the sphere feature was intentionally disabled, not forgotten

---

## Dependencies
**Blocked by**: none
**Blocks**: cleanup task (if one is created from this research)
**Related tasks**: 2026-07-12 HIGH bug-fix — biome canvas rendering (prerequisite — fix applied first)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### Findings Summary
[One-line summary of what was found]

### Recommended Actions
1. [Action 1]
2. [Action 2]

### Follow-up Tasks Needed
[Any new backlog items identified]

---

## Handoff Summary
RESEARCH — audit all monitor.js backup files and sphere CRUD dead code. Deliverable is a research report with recommended actions, not code changes.
