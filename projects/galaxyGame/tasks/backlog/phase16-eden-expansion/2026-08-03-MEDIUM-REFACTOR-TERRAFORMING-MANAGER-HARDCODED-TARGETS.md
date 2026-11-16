---
status: backlog
priority: MEDIUM
category: REFACTOR
created: 2026-08-03
updated: 2026-08-03
estimated_effort: 1-2 hours
blocker_for: []
---

# Task: Remove Hardcoded Atmospheric Targets in TerraformingManager

## Context

**Byproduct of**: `2026-07-24-MEDIUM-REFACTOR-TERRAFORMING-MANAGER-CLEANUP.md` (completed 2026-08-03)
**Source**: Completion report flagged hardcoded atmospheric targets scattered throughout the codebase beyond what the prior cleanup touched.

**Problem**: The prior task made TerraformingManager world-agnostic for parameter names and comments, but hardcoded atmospheric target values (pressures, thresholds, etc.) remain in the code that should come from world data/templates.

## Architecture Gotchas

⚠️ **GOTCHA 1: Start with read-only audit — do NOT skip**
- ❌ Wrong: Assume all hardcoded values are bugs to remove
- ✅ Right: Perform a read-only inventory first — identify which values are reasonable defaults vs. which should be data-driven
- Why: Some hardcoded values may be sensible fallbacks that don't need to be data-driven (e.g., physical constants, safety limits)

⚠️ **GOTCHA 2: Distinguish between "default" and "hardcoded"**
- Defaults are acceptable when they're truly fallbacks for edge cases
- Hardcoded values that override world-specific data are the problem
- Must trace each value to determine which category it belongs to

## Files Involved

| File | Purpose |
|------|---------|
| `galaxy_game/app/services/ai_manager/terraforming_manager.rb` | Primary file — hardcoded targets live here |
| Other AI Manager services that may have similar hardcoding | Read-only audit scope |

## Implementation Steps

### Step 0: Move Task to Active & Verify
1. Move task from `backlog/current/` → `active/`
2. Update YAML header: `status: backlog` → `status: active`
3. Commit move before writing any code
4. Verify with `find agent-tasks/projects/galaxy_game/tasks -name "2026-08-03-MEDIUM-REFACTOR-TERRAFORMING-MANAGER-HARDCODED-TARGETS.md"` — only one result

### Step 1: Read-Only Inventory
```bash
grep -n "pressure\|threshold\|target" galaxy_game/app/services/ai_manager/terraforming_manager.rb | grep -v "def \|comment\|# " | head -40
```
Document all hardcoded atmospheric targets with line numbers and values.

### Step 2: Classify Each Value
For each hardcoded value found:
- Is it a **reasonable default** (fallback for edge cases)? → Keep with comment
- Is it a **world-specific override** that should come from data? → Fix
- Is it a **physical constant** or **safety limit**? → Keep, document

### Step 3: Fix World-Specific Overrides
For values that should be data-driven:
- Remove the hardcoded value
- Add fallback to sensible default (e.g., `|| 21.0` for O2 threshold)
- Add comment noting the value should come from world template

### Step 4: Verification
- All world-specific overrides removed or justified with data-driven alternatives
- Reasonable defaults preserved with explanatory comments
- No behavioral changes to existing callers

## Synthesis Report

**[SYNTHESIS REPORT TEMPLATE — Fill this in BEFORE making code changes]**

**Objective**: Classify all hardcoded atmospheric targets as reasonable defaults vs. world-specific overrides that need fixing.

**Steps**:
1. Document all hardcoded values with line numbers and context
2. For each value, determine: is it a default (keep) or override (fix)?
3. Identify which world templates should provide each value

**Document findings before proceeding**:
```
## Synthesis Findings

### Hardcoded values found:
| Line | Value | Context | Classification |
|------|-------|---------|----------------|
| X | Y | [what it's used for] | default/override |

### Values that should come from world templates:
[list values + which template field]

### Reasonable defaults to keep:
[list values + why they're acceptable as defaults]
```

---

## Acceptance Criteria

- [ ] All hardcoded atmospheric targets classified (default vs override)
- [ ] World-specific overrides removed or justified with data-driven alternatives
- [ ] Reasonable defaults preserved with explanatory comments
- [ ] No behavioral changes to existing callers

## Stop Conditions

- Escalate to NEEDS_REVIEW.md if fixing one value reveals the others are more architecturally entangled than they appear
- Do NOT proceed without completing Step 1 read-only inventory first

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

**For next agent**: This is a medium-scope refactor — classify all hardcoded values before making changes. Some are reasonable defaults, some are world-specific overrides that need fixing. Step 1 read-only inventory is mandatory.
