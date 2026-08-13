---
status: backlog
priority: MEDIUM
type: refactor
created: 2026-08-05
last_updated: 2026-08-05
assigned_to: null
discovered_in: wvu_knapsack
---

# TASK: Add Defensive Check for Wings::ModelRegistry Constant

## ⚡ Minimal Handoff

```
You are Implementation Agent.

Project: samvera_hyku
Task: /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/backlog/2026-08-05-MEDIUM-REFACTOR-WINGS-MODELREGISTRY-DEFENSIVE-CHECK.md

STEP 0 — MOVE TASK FILE (before anything else):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/samvera_hyku/tasks/backlog/2026-08-05-MEDIUM-REFACTOR-WINGS-MODELREGISTRY-DEFENSIVE-CHECK.md \
         projects/samvera_hyku/tasks/active/2026-08-05-MEDIUM-REFACTOR-WINGS-MODELREGISTRY-DEFENSIVE-CHECK.md
  Then edit the file and change: status: backlog → status: active
  Paste output before proceeding.

READ FIRST: Full task file at the above path. Contains all prerequisites, code locations, and acceptance criteria.

WORKFLOW: Create GitHub issue in samvera/hyku → Create feature branch → Implement fix → Open PR → Merge
```

---

## Context

**Discovered in**: wvu_knapsack session (2026-08-05) while investigating console errors
**Scope**: Community improvement (benefits all Hyku instances)
**Motivation**: Defensive robustness — prevent NameError if Wings::ModelRegistry fails to load

---

## What This Is

A small but important **defensive improvement** to Hyku's Wings integration that adds a safety check to prevent a `NameError` in the resource class resolver.

**Current code** (hyrax-webapp/config/initializers/wings.rb, ~line 202):
```ruby
Valkyrie.config.resource_class_resolver = lambda do |resource_klass_name|
  klass = resource_klass_name.gsub(/Resource$/, '').constantize
  Wings::ModelRegistry.reverse_lookup(klass) || klass
end
```

**Proposed fix**:
```ruby
Valkyrie.config.resource_class_resolver = lambda do |resource_klass_name|
  klass = resource_klass_name.gsub(/Resource$/, '').constantize
  if defined?(Wings::ModelRegistry)
    Wings::ModelRegistry.reverse_lookup(klass) || klass
  else
    klass
  end
end
```

**Why it matters**:
- `Wings::ModelRegistry` should always be loaded when this resolver runs (Wings is required in the parent block)
- However, defensive code is a good pattern: if for any reason Wings fails to load, the resolver gracefully falls back to the base class instead of crashing
- This pattern improves robustness across all Hyku instances without changing normal behavior

---

## Acceptance Criteria

- [ ] GitHub issue opened in samvera/hyku describing the improvement
- [ ] Feature branch created (e.g., `refactor/wings-modelregistry-defensive-check`)
- [ ] Change implemented in hyrax-webapp/config/initializers/wings.rb
- [ ] Test added to verify fallback behavior (optional but preferred: test that resolver works when Wings is not defined)
- [ ] PR opened with clear explanation of why defensive check is valuable
- [ ] PR merged to main branch
- [ ] wvu_knapsack submodule reference updated to latest Hyku main

---

## Files to Modify

**Repository**: samvera/hyku (hyrax-webapp submodule)
**File**: `config/initializers/wings.rb`
**Section**: `resource_class_resolver` lambda (around line 195-207)

---

## Implementation Notes

### Before You Start
1. Verify this change doesn't already exist in the latest Hyku main branch
2. Check Hyku's existing test coverage for `resource_class_resolver`
3. Open GitHub issue in samvera/hyku with label `type: refactor`

### Testing Strategy
- The change is small and defensive — existing behavior unchanged when Wings is loaded
- If possible, write a test that mocks `Wings::ModelRegistry` as undefined and verifies the resolver returns the base class
- Run existing spec suite to ensure no regressions

### PR Notes
- Title: "refactor: add defensive check for Wings::ModelRegistry in resource_class_resolver"
- Explain: This prevents a potential NameError if Wings fails to load (unlikely but good defensive pattern)
- Link: Reference that this improves robustness for all Hyku instances

---

## Why This Belongs in Hyku (Not Knapsack)

✅ **Should be in Hyku** because:
- It's a framework-level improvement (not WVU-specific)
- Benefits ALL Hyku instances
- Reduces maintenance burden (single source of truth upstream)
- Better to propagate improvements up the chain than down

❌ **Should NOT be in Knapsack** because:
- Knapsack is for local customizations, not upstream fixes
- Duplicating it in Knapsack creates fragmentation and future merge conflicts

---

## Dependency Chain

```
wvu_knapsack (discovered issue here)
  ← hyrax-webapp (this change goes here)
    ← samvera/hyku (main repo)
```

Once merged to Hyku main, the fix flows down to all dependent instances via submodule updates.

---

## Next Steps After Completion

1. Merge PR to samvera/hyku main
2. Update wvu_knapsack submodule reference: `git submodule update --remote hyrax-webapp`
3. Verify fix is present: `git log hyrax-webapp --oneline | grep -i wings`
4. Test locally: `sh up.sc.local.sh` and verify no Wings-related errors

---

## Reference Links

- **Hyku repo**: https://github.com/samvera/hyku
- **Current wings.rb**: https://github.com/samvera/hyku/blob/main/hyrax-webapp/config/initializers/wings.rb
- **Original discovery**: wvu_knapsack session 2026-08-05 (console error investigation)
