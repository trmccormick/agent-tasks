---
status: backlog
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY FOR DISPATCH.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/backlog/2026-08-25-HIGH-BUGFIX-FACET-LIMITING-FILTERED-RESULTS.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvulibraries_knapsack/tasks/backlog/2026-08-25-HIGH-BUGFIX-FACET-LIMITING-FILTERED-RESULTS.md \
         projects/wvulibraries_knapsack/tasks/active/2026-08-25-HIGH-BUGFIX-FACET-LIMITING-FILTERED-RESULTS.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find projects/wvulibraries_knapsack/tasks -name "2026-08-25-HIGH-BUGFIX-FACET-LIMITING-FILTERED-RESULTS.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

---

# TASK: Facet Limiting on Filtered Results — Investigation & Fix

**Status**: BACKLOG  
**Priority**: HIGH  
**Type**: bug-fix  
**Created**: 2026-08-25  
**Last Updated**: 2026-08-25

---

## Problem Statement

Facet limiting works correctly on the main catalog page (max 5 items per facet), but **fails on filtered results pages** when active facet filters are applied.

### Current Behavior

**✅ WORKING** (main catalog):
```
https://demo-wvu-knapsack.localhost.direct/catalog?locale=en
```
- All facets show exactly 5 items max
- "More" links appear for facets with >5 items
- Solr request includes `f.{field_name}.facet.limit=5` params

**❌ BROKEN** (filtered results):
```
https://demo-wvu-knapsack.localhost.direct/catalog?f%5Bpeople_represented_sim%5D%5B%5D=West%2C+Jerry%2C+1938-2024&locale=en
```
- Facets show 10+ items (limit NOT applied)
- "More" links do not appear
- Root cause unknown (params missing OR Solr ignoring them)

### Code Context

**Implementation**: `app/search_builders/catalog_search_builder_wrapper.rb`
- Wrapper extends AdvSearchBuilder, overrides `build(user_params)` method
- Adds Solr-level `f.{field_name}.facet.limit` params from Blacklight config
- Pattern: Proven working on homepage (HomepageSearchBuilderWrapper does same thing)

**Injection**: `app/controllers/catalog_controller_decorator.rb`
- Sets `search_builder_class = CatalogSearchBuilderWrapper` 
- Configures `facet_config.limit = 5` for all facets

**Initialization**: `config/initializers/999_catalog_controller_decorator.rb`
- Applies decorator via `Rails.application.config.after_initialize` hook

---

## Task Objectives

**Primary Goal**: Identify why facet limiting fails on filtered results pages

**Success Criteria**:
1. Confirm whether `CatalogSearchBuilderWrapper.build()` is called for filtered searches
2. Verify whether facet.limit params are present in Solr request for filtered searches
3. If missing: identify where they're lost
4. If present: identify why Solr is ignoring them
5. Propose patch recommendation or document limitation

---

## Implementation Steps

### Step 1: Add Debug Logging
Edit `app/search_builders/catalog_search_builder_wrapper.rb` and add logging:

```ruby
def build(user_params = {})
  result = super
  puts "🔍 WRAPPER BUILD DEBUG:"
  puts "   User params: #{user_params.inspect}"
  puts "   Final Solr params facet.limit: #{result.select { |k,v| k.to_s.include?('facet.limit') }}"
  result
end
```

Commit this logging:
```bash
git add app/search_builders/catalog_search_builder_wrapper.rb
git commit -m "debug: add facet-limit logging to wrapper"
```

### Step 2: Test Both Scenarios
Visit both test URLs and capture logs:

```bash
# Terminal 1: Watch logs
sc logs web 2>&1 | grep -i "wrapper\|facet.limit"

# Terminal 2: Make requests
curl -s 'https://demo-wvu-knapsack.localhost.direct/catalog?locale=en' -k > /dev/null
# Wait for logs...

curl -s 'https://demo-wvu-knapsack.localhost.direct/catalog?f%5Bpeople_represented_sim%5D%5B%5D=West%2C+Jerry%2C+1938-2024&locale=en' -k > /dev/null
# Wait for logs...
```

### Step 3: Analyze Logs
Compare the Solr parameters logged for both URLs:
- **Main page**: Should show `facet.limit=5` params
- **Filtered page**: Check if `facet.limit=5` params present

### Step 4: Root Cause Investigation

**If facet.limit params are MISSING from filtered request**:
- Check if CatalogSearchBuilderWrapper.build() is called at all
- Verify search_builder_class still points to wrapper for filtered queries
- Check if there's a separate code path for filtered searches
- Look for any override in CatalogController or parent classes

**If facet.limit params are PRESENT but ignored by Solr**:
- Verify Solr accepts `f.{field_name}.facet.limit` params (it should)
- Check if Solr config has conflicting facet settings
- Verify facet field names match exactly between Blacklight config and Solr schema

### Step 5: Document Findings
Create synthesis report with:
- Problem statement
- Investigation steps performed
- Debug log examples (main page vs filtered page)
- Root cause analysis
- Proposed fix or known limitation
- Next steps for deployment

---

## Acceptance Criteria

- [ ] Investigated whether facet.limit params present in filtered search Solr request
- [ ] Root cause identified (missing params, wrong code path, or Solr config issue)
- [ ] Patch recommendation provided OR known limitation documented
- [ ] Synthesis report saved at: `projects/wvulibraries_knapsack/summaries/2026-08-25-BUGFIX-FACET-LIMITING-FILTERED-INVESTIGATION.md`
- [ ] Debug logging removed or wrapped in Rails.env.development check
- [ ] Task file moved to completed/ with status: completed
- [ ] Commit created and pushed to origin/main

---

## Architecture Gotchas

**⚠️ Wrapper Initialization Timing**:
- The decorator must be applied via `after_initialize` hook (in 999_catalog_controller_decorator.rb)
- Applying via `to_prepare` or direct `prepend` fails because CatalogController not yet defined or Wings module not ready
- Current implementation is correct (confirmed by homepage working)

**⚠️ Search Builder Chain**:
- CatalogSearchBuilderWrapper extends AdvSearchBuilder (from Blacklight)
- AdvSearchBuilder is the base for all Blacklight search builders
- Must call `super` to preserve the complete builder chain
- Current implementation does this correctly

**⚠️ Solr Parameter Format**:
- Solr facet limiting uses `f.{field_name}.facet.limit` format (NOT `facet_limit` or `facetLimit`)
- This is standard Solr format, proven working on main page
- Verify exact field names in facet_config match Solr schema

**⚠️ Test URL Encoding**:
- URL provided uses URL-encoded brackets and spaces: `f%5B`, `%5D`, `+`
- These decode to `f[`, `]`, and space in browser
- Use curl with `-k` flag (insecure SSL for localhost.direct)

**⚠️ Rails Logs**:
- Stack Car logs: `sc logs web 2>&1`
- Filter for wrapper output: `grep -i "wrapper\|facet"`
- Note: puts output may appear interleaved with other logs
- Use timestamps to align logs with requests

---

## Related Work

**Previous Task** (COMPLETED):
- 2026-08-25-HIGH-BUGFIX-FACET-LIMITING-HTML-CATALOG-TEST.md
- Result: Main page facet limiting ✅ WORKS
- Synthesis: projects/wvulibraries_knapsack/summaries/2026-08-25-FACET-LIMITING-HTML-TEST-RESULTS.md

**Blocking Issue**:
- Cannot deploy facet-limiting feature until filtered results are fixed
- Main page alone is not sufficient for production use

**Branch**: fix/catalog-facet-limiting-solr-level (all code committed, ready)

---

## References

- **CatalogSearchBuilderWrapper**: app/search_builders/catalog_search_builder_wrapper.rb
- **CatalogControllerDecorator**: app/controllers/catalog_controller_decorator.rb
- **Initializer**: config/initializers/999_catalog_controller_decorator.rb
- **Status**: projects/wvulibraries_knapsack/status.md
- **Synthesis Template**: See "CRITICAL: Save synthesis report" in Agent Dispatch Interface above
