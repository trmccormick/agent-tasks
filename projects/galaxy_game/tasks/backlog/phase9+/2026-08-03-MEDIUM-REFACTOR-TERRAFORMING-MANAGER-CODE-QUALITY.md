---
status: backlog
priority: MEDIUM
category: REFACTOR
created: 2026-08-03
updated: 2026-08-03
estimated_effort: 2-3 hours
blocker_for: []
---

# Task: TerraformingManager Code Quality — Fix `default_params` Bug, Method Shadowing, Hardcoded Targets

## Context

**Byproduct of**: `2026-07-24-MEDIUM-REFACTOR-TERRAFORMING-MANAGER-CLEANUP.md` (completed 2026-08-03)
**Source**: Issues surfaced during the prior task's completion report — scoped as follow-up, not blockers.

**Problem Statement:**

1. **`default_params` double-definition bug**: Two `def default_params` methods exist in the same file. The second silently overrides the first with no warning, meaning any values only set in the first definition are dead code.
2. **Private/public method naming collision**: Private `calculate_warming_phase_needs` and `calculate_maintenance_phase_needs` shadow public methods with the same names. Needs investigation into which method actually gets called at runtime vs which was intended.
3. **Hardcoded atmospheric targets** remain scattered elsewhere in the codebase beyond what the prior cleanup touched.

## Architecture Gotchas

⚠️ **GOTCHA 1: Start with read-only audit — do NOT skip (per prior task)**
- ❌ Wrong: Assume all duplication is accidental vs. intentional fallback without checking call sites first
- ✅ Right: Perform a read-only method inventory before any edits — identify which methods are called from where, then determine if "duplicates" are intentional fallback paths
- Why: The prior task deliberately skipped this audit and proceeded to refactoring; that was the correct call for the data-driven cleanup scope but repeating it here would risk breaking intentional fallback behavior

⚠️ **GOTCHA 2: `default_params` double-definition is a Ruby gotcha**
- Ruby silently uses the last definition — no compiler warning, no runtime error
- The first definition's values are dead code but may have been relied upon by callers who never noticed because they worked with the second definition's values
- Fixing this requires identifying which values from the first definition are actually needed vs truly dead

⚠️ **GOTCHA 3: Method shadowing may be intentional**
- Ruby allows private methods to shadow public ones within the same class
- This could be a bug (accidental naming) or intentional (private override for internal use)
- Must trace call sites before deciding whether to rename, merge, or document

## Files Involved

| File | Purpose |
|------|---------|
| `galaxy_game/app/services/ai_manager/terraforming_manager.rb` | Primary file — all three issues live here |
| Callers of TerraformingManager (AI Manager services) | Read-only audit to verify which methods are actually invoked |

## Implementation Steps

### Step 0: Move Task to Active & Verify
1. Move task from `backlog/current/` → `active/`
2. Update YAML header: `status: backlog` → `status: active`
3. Commit move before writing any code
4. Verify with `find agent-tasks/projects/galaxy_game/tasks -name "2026-08-03-MEDIUM-REFACTOR-TERRAFORMING-MANAGER-CODE-QUALITY.md"` — only one result

### Step 1: Read-Only Method Audit (MANDATORY FIRST STEP)
**Do NOT skip this step.** This is the audit that was skipped in the prior task.

```bash
# In galaxyGame repo:
grep -n "def.*default_params\|def.*warming_phase_needs\|def.*maintenance_phase_needs" galaxy_game/app/services/ai_manager/terraforming_manager.rb
grep -rn "TerraformingManager.new\|TerraformingManager\.new" galaxy_game/app/ --include="*.rb" | head -20
```

Document:
- Exact line numbers of both `default_params` definitions and their contents
- Which method (private or public) gets called at runtime for each call site
- All hardcoded atmospheric targets with file/line locations

### Step 2: Fix `default_params` Double-Definition
Based on audit findings:
- Merge the two definitions into one if both sets of values are needed
- Or remove the dead definition and document which values were truly unused
- Add a comment noting this was previously double-defined (historical context)

### Step 3: Resolve Method Shadowing
Based on audit findings:
- If shadowing is accidental: rename private methods to avoid collision (e.g., `calculate_internal_warming_phase_needs`)
- If intentional: add documentation explaining why the shadowing exists
- Verify no call sites are broken by the change

### Step 4: Remove Hardcoded Atmospheric Targets
Based on audit findings from Step 1:
- List all hardcoded targets with file/line
- Determine which should come from world data/templates vs. which are reasonable defaults
- Apply fixes consistent with the prior task's data-driven approach

### Step 5: Verification
- Run `grep -n "def.*default_params" galaxy_game/app/services/ai_manager/terraforming_manager.rb` — should show exactly one definition
- Verify no method shadowing remains (or is documented as intentional)
- Confirm all hardcoded atmospheric targets are either removed or justified with data-driven alternatives

## Synthesis Report

**[SYNTHESIS REPORT TEMPLATE — Fill this in BEFORE making code changes]**

**Objective**: Confirm understanding of the three issues and their interdependencies.

**Steps**:
1. Read `terraforming_manager.rb` fully — document both `default_params` definitions with line numbers and contents
2. Trace all callers of TerraformingManager to verify which methods are actually invoked at runtime
3. Identify all hardcoded atmospheric targets in the file (beyond what the prior task already fixed)
4. Determine if fixing one issue affects the others (e.g., does merging `default_params` change which values shadowing depends on?)

**Document findings before proceeding to implementation**:
```
## Synthesis Findings

### default_params Definitions
- First definition at line X: [contents]
- Second definition at line Y: [contents]
- Dead values (only in first): [...]
- Values used by callers: [...]

### Method Shadowing
- Public method at line X: [name, purpose]
- Private method at line Y: [name, purpose]
- Which gets called at each call site: [...]

### Hardcoded Targets
- File/line: [target value, what it should be]
```

---

## Acceptance Criteria

- [ ] Exactly one `default_params` definition in the file (verified by grep)
- [ ] No private/public method naming collision (or documented as intentional with rationale)
- [ ] All hardcoded atmospheric targets either removed or justified with data-driven alternatives
- [ ] No call sites broken — verified by tracing callers
- [ ] Synthesis report filled in before implementation

## Stop Conditions

- Escalate to NEEDS_REVIEW.md if the naming collision turns out to affect live simulation behavior (not just dead/duplicate code)
- Escalate if fixing one issue reveals the other two are more architecturally entangled than they appear
- Do NOT proceed with fixes without completing Step 1 audit first

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

**For next agent**: Complete Step 1 audit before any edits. The prior task's completion report flagged three issues — this task exists solely to address them with proper audit-first methodology. Do NOT skip the read-only audit step.
