# Task: Fix Deprecation Warnings and Strong Parameters Issues

**Status**: BACKLOG  
**Priority**: MEDIUM (Low priority, cleanup only)  
**Created**: 2026-08-26  
**Assigned to**: Qwen (future session)  

---

## Problem Statement

Two warning categories appearing in Rails logs during catalog navigation:

### 1. Unpermitted Parameter Warning
Message: "Unpermitted parameter: :locale. Context: { controller: CatalogController, action: index, request: #..., params: {"locale"=>"en", "controller"=>"catalog", "action"=>"index"} }"

Issue: CatalogController not whitelisting locale param in strong parameters. Locale still works (passed through), but generates log noise.

### 2. Blacklight Deprecation Warning
Message: "DEPRECATION WARNING: add_facet_params is deprecated and will be removed from a future release (Use filter(field).add(item) instead). (called from block in add_facet_params_and_redirect at /usr/local/bundle/gems/blacklight-7.42.0/lib/blacklight/search_state.rb:145)"

Issue: Blacklight 7.42.0 deprecating add_facet_params method. Code likely calling deprecated method somewhere in facet linking logic.

---

## Acceptance Criteria

Investigation + fixes MUST produce:

1. **Locale Parameter Fix**:
   - CatalogController permits :locale param properly
   - No more "Unpermitted parameter: :locale" warnings in logs
   - Locale parameter continues to work

2. **Blacklight Deprecation Fix**:
   - Identify where add_facet_params is being called
   - Replace with filter(field).add(item) pattern (or newer Blacklight API)
   - Test facet linking still works after change
   - Deprecation warning no longer appears

3. **No Regressions**:
   - All facet-limiting work unaffected
   - Homepage and catalog facets still function
   - No new warnings introduced

---

## Investigation Steps

### STEP 0: Prepare Environment
- Move task from backlog/ to active/ via git mv
- Create synthesis file path: projects/wvulibraries_knapsack/summaries/2026-08-26-CLEANUP-DEPRECATION-FIXES.md

### STEP 1: Fix Locale Parameter
Check CatalogController strong parameters:
grep -n "permit" app/controllers/catalog_controller_decorator.rb
grep -n "permit" hyrax-webapp/app/controllers/hyrax/catalog_controller.rb

Look for: params.require(:q) or similar. Add :locale to the permit list.

Test after fix:
curl -s 'https://demo-wvu-knapsack.localhost.direct/catalog?locale=en' -k 2>&1 | grep -i "unpermitted parameter"

If no warning appears in logs, fix is confirmed.

### STEP 2: Find add_facet_params Usage
Search for deprecated method:
grep -rn "add_facet_params" app/ lib/ config/ --include="*.rb"

Check which files reference it. Likely candidates:
- app/controllers/facets_controller.rb or similar
- app/search_builders/ (search builder logic)
- Any custom facet linking code

### STEP 3: Understand Blacklight Migration
Check Blacklight 7.42.0 documentation or source:
What's the new API pattern? (filter(field).add(item))

Review git history for facet-related changes:
git log --oneline -20 | grep -i facet

### STEP 4: Apply Fixes
Replace deprecated calls with new API. Test facet linking:
1. Homepage: Click facet value to filter
2. Catalog: Click facet value to filter
3. Filtered page: Verify filters still apply and display

Log should not show deprecation warning after change.

### STEP 5: Verify No Regressions
Run through all facet-limiting feature tests:
- Homepage facets limit to 5 ✓
- Catalog facets (the 3 that work) still limit ✓
- "More" links still functional ✓
- No new errors or warnings introduced ✓

---

## Synthesis Requirements

Create file: projects/wvulibraries_knapsack/summaries/2026-08-26-CLEANUP-DEPRECATION-FIXES.md

Format:
- **Issue 1 (Locale)**: Root cause + fix applied
- **Issue 2 (Deprecation)**: Root cause + API replacement pattern + fix applied
- **Testing**: Confirmation that warnings cleared + no regressions
- **Files Modified**: List all changed files with line numbers

---

## Context

Environment: M4 Mac, Stack Car (https://demo-wvu-knapsack.localhost.direct)
Stack: Hyku 7.1.0 + Hyrax 5.2.0, Rails 7.2.3, Ruby 3.3.10, Blacklight 7.42.0
Branch: fix/hide-type-facet-add-show-more-facets
Log: Both warnings visible in Rails server logs during catalog navigation

---

## Notes

- Not blocking current facet-limiting feature (feature works despite warnings)
- Secondary to facet config investigation and images issue
- Cleanup task only (no impact on functionality, just noise reduction)
- Token conservation: Can be deferred to later session if needed
