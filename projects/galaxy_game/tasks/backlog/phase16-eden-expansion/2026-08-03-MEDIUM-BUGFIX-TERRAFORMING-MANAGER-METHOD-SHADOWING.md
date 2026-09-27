---
status: superseded
priority: MEDIUM
category: BUGFIX
created: 2026-08-03
updated: 2026-08-03
estimated_effort: 1-2 hours
blocker_for: []
superseded_by: 2026-08-03-HIGH-BUGFIX-TERRAFORMING-MANAGER-METHOD-SHADOWING.md
---

# Task: Resolve Private/Public Method Shadowing in TerraformingManager

## Context

**Byproduct of**: `2026-07-24-MEDIUM-REFACTOR-TERRAFORMING-MANAGER-CLEANUP.md` (completed 2026-08-03)
**Source**: Completion report flagged private/public method naming collision — private `calculate_warming_phase_needs` and `calculate_maintenance_phase_needs` shadow public methods with the same names.

**Problem**: Ruby allows private methods to shadow public ones within the same class. This could be a bug (accidental naming) or intentional (private override for internal use). Needs investigation into which method actually gets called at runtime vs which was intended.

## Architecture Gotchas

⚠️ **GOTCHA 1: Shadowing may be intentional**
- ❌ Wrong: Assume shadowing is always a bug to fix
- ✅ Right: Trace call sites first — determine if the private override serves a specific purpose (e.g., internal calculation with different logic)
- Why: Removing "accidental" shadowing could break internal calculations that rely on the private version

⚠️ **GOTCHA 2: This is higher risk than the default_params fix**
- Method shadowing affects runtime behavior, not just data defaults
- Must verify no live simulation paths are affected before making changes

## Files Involved

| File | Purpose |
|------|---------|
| `galaxy_game/app/services/ai_manager/terraforming_manager.rb` | Primary file — shadowed methods live here |
| Callers of TerraformingManager (AI Manager services) | Read-only audit to verify which method gets called at runtime |

## Implementation Steps

### Step 0: Move Task to Active & Verify
1. Move task from `backlog/current/` → `active/`
2. Update YAML header: `status: backlog` → `status: active`
3. Commit move before writing any code
4. Verify with `find agent-tasks/projects/galaxy_game/tasks -name "2026-08-03-MEDIUM-BUGFIX-TERRAFORMING-MANAGER-METHOD-SHADOWING.md"` — only one result

### Step 1: Identify Shadowed Methods
```bash
grep -n "def.*warming_phase_needs\|def.*maintenance_phase_needs" galaxy_game/app/services/ai_manager/terraforming_manager.rb
```
Document exact line numbers, visibility (private/public), and method contents for each.

### Step 2: Trace Call Sites
```bash
grep -rn "calculate_warming_phase_needs\|calculate_maintenance_phase_needs" galaxy_game/app/ --include="*.rb" | head -30
```
Determine which version gets called at each call site (private or public).

### Step 3: Determine Intent
- If shadowing is accidental: rename private methods to avoid collision (e.g., `calculate_internal_warming_phase_needs`)
- If intentional: add documentation explaining why the shadowing exists and what it's for
- If both serve different purposes: keep both but rename to clarify intent

### Step 4: Verification
- No method shadowing remains (or is documented as intentional with rationale)
- All call sites verified — no runtime behavior changes

## Synthesis Report

**[SYNTHESIS REPORT TEMPLATE — Fill this in BEFORE making code changes]**

**Objective**: Confirm whether the private/public naming collision is accidental or intentional.

**Steps**:
1. Document both public and private methods with line numbers and full contents
2. Trace all call sites to verify which version gets called at runtime
3. Determine if the private version serves a specific purpose (different logic, internal use only)

**Document findings before proceeding**:
```
## Synthesis Findings

### Public method at line X:
[name, purpose, key differences from private]

### Private method at line Y:
[name, purpose, key differences from public]

### Which gets called at each call site:
[caller file/line → which version]

### Evidence of intent:
[comments, git history, or logic that suggests intentional vs accidental]
```

---

## Acceptance Criteria

- [ ] No private/public method naming collision (or documented as intentional with rationale)
- [ ] All call sites verified — no runtime behavior changes
- [ ] Method names clarify intent (no ambiguity about which version is called)

## Stop Conditions

- Escalate to NEEDS_REVIEW.md if the naming collision turns out to affect live simulation behavior (not just dead/duplicate code)
- Escalate if fixing this reveals the other two issues are more architecturally entangled than they appear

## Completion Report

**[FILL IN AFTER COMPLETION]**

### What Was Done
- [Describe changes made]

### Issues Discovered During Work
- [Any unexpected findings]

### Follow-up Tasks
- [Any remaining work]

### Lessons Learned
- [Any insights for future sessions]

---

## Handoff Summary

**For next agent**: This is a medium-risk bugfix — method shadowing affects runtime behavior. Must trace call sites carefully before making changes. If shadowing is intentional, document it; if accidental, rename to avoid collision.
