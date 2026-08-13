# Investigate Wings::ModelRegistry NameError Fix

**Status:** Investigation Complete — Fix Already Implemented  
**Priority:** Medium  
**Source:** WVU Knapsack soft launch (identified 2026-08-10)  
**Investigation Date:** 2026-08-11  

## Problem
Hyrax::Goddess::Query#model_class_for throws `NameError: uninitialized constant Wings::ModelRegistry` during work show page rendering when Wings::ModelRegistry is not defined.

## Current Workaround in Knapsack
Location: `config/initializers/goddess_query_fix.rb`
```ruby
if defined?(Hyrax::Goddess::Query)
  Hyrax::Goddess::Query.class_eval do
    def model_class_for(model)
      internal_resource = model.respond_to?(:internal_resource) ? model.internal_resource : nil
      return internal_resource.safe_constantize if internal_resource&.safe_constantize
      
      if defined?(Wings::ModelRegistry)
        Wings::ModelRegistry.lookup(model)
      else
        model.is_a?(Class) ? model : model.class
      end
    end
  end
end
```

## Investigation Findings (2026-08-11)
✅ **Fix already implemented in Hyku** — commit `e20f31e9` on branch `fix/ga-tenant-property-scoping`.

**Implementation**: `lib/hyrax/goddess/query_method_missing_machinations_decorator.rb`
- Prepends decorator to `Goddess::Query::MethodMissingMachinations#model_class_for`
- Guards `Wings::ModelRegistry.lookup` with `defined?()` check
- Falls back to safe constant resolution when Wings is disabled
- Uses `.prepend()` pattern (correct for Hyku decorators)

**Ownership**: Hyku — this is a Hyku-specific decorator, not a Hyrax core change.

## Tasks
- [x] Check if Hyku main has already fixed this (PR search)
- [x] If not fixed, investigate root cause in Wings or Hyrax
- [x] Determine if fix should go to Hyku, Hyrax, or Wings
- [ ] Prepare upstream PR — **needs separate branch** (not on GA analytics branch)

## Branch Separation Required
The current branch `fix/ga-tenant-property-scoping` bundles two distinct pieces of work:
- 6 files → GA multi-tenant analytics (Phase 1)
- 1 file → Wings::ModelRegistry fix

**Recommended**: Cherry-pick the Wings fix onto a new branch:
```bash
git checkout -b fix/wings-modelregistry-guard main
git cherry-pick e20f31e9
```

## GitHub Issue Status
**No existing issue found.** Searched:
- Hyku task files — no references
- Commit message `e20f31e9` — no issue number
- Knapsack workaround code — no issue reference

**Action needed**: File a new issue on the Hyku repo before submitting the PR. Recommended title:
> "NameError: uninitialized constant Wings::ModelRegistry when Wings is disabled"

Include in the issue:
- Reproduction steps (Bulkrax importer form fails with `disable_wings = true`)
- Root cause (Hyrax Goddess::Query unconditionally references Wings::ModelRegistry)
- Fix reference (Hyku decorator pattern, commit `e20f31e9` on feature branch)
- Impact (affects any deployment with Wings disabled, e.g., Bulkrax importer UI)

## Impact
- Affects all Hyku instances with conditional Wings::ModelRegistry availability
- Defensive check prevents NameError on initialization
- Small defensive patch suitable for upstream contribution

## Notes
- Used in production (hykudev) successfully
- Minimal change, backwards compatible
- Consider for Hyku 7.1.x patch release
