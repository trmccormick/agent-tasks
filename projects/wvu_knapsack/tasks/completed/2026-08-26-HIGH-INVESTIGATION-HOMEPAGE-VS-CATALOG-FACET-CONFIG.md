---
status: completed
priority: HIGH
type: investigation
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
synthesis_report: projects/wvulibraries_knapsack/summaries/2026-08-26-INVESTIGATION-FACET-CONFIG-DISCREPANCY.md
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
You are **Investigation Agent**.

Project: wvulibraries_knapsack
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/backlog/2026-08-26-HIGH-INVESTIGATION-HOMEPAGE-VS-CATALOG-FACET-CONFIG.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvulibraries_knapsack/tasks/backlog/2026-08-26-HIGH-INVESTIGATION-HOMEPAGE-VS-CATALOG-FACET-CONFIG.md \
         projects/wvulibraries_knapsack/tasks/active/2026-08-26-HIGH-INVESTIGATION-HOMEPAGE-VS-CATALOG-FACET-CONFIG.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find projects/wvulibraries_knapsack/tasks -name "2026-08-26-HIGH-INVESTIGATION-HOMEPAGE-VS-CATALOG-FACET-CONFIG.md"
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

# TASK: Homepage vs Catalog Facet Config Discrepancy Investigation

**Status**: BACKLOG  
**Priority**: HIGH  
**Type**: investigation  
**Created**: 2026-08-26  
**Last Updated**: 2026-08-26

---

## ⚡ MINIMAL HANDOFF

**What's wrong**: Facet limiting is inconsistent:
- **Homepage**: ALL facets limited to 5 items ✅ (Creator, Subject, Collection Type, Location, Keyword, etc.)
- **Catalog view** (`/catalog?locale=en`): ONLY 3 facets limited (Creator, Subject, Collection Type) ❌

**Why it matters**: We applied facet limiting via `CatalogControllerDecorator`, but it only works on homepage, not catalog. Need to understand why.

**Investigation task**:
1. Identify which controller/view renders homepage facets
2. Identify which controller/view renders catalog facets  
3. Determine if they use different configurations or code paths
4. Find why the decorator doesn't apply uniformly
5. Document findings + recommend fix approach

**Output**: Synthesis report at `projects/wvulibraries_knapsack/summaries/2026-08-26-INVESTIGATION-FACET-CONFIG-DISCREPANCY.md`

---

## Problem Statement

**Observed Behavior**

Homepage facet sidebar (`/`):
- Shows exactly 5 items per facet (Creator, Subject, Collection Type, Location, Keyword, Contributing Library, etc.)
- "More" links appear for facets with >5 items
- ALL configured facets are limited uniformly

Catalog search facet sidebar (`/catalog?locale=en` & filtered pages):
- Creator: limited to 5 items ✅
- Subject: limited to 5 items ✅  
- Collection Type: limited to 5 items ✅
- Resource Type: unlimited (shows all) ❌
- Contributor: unlimited (shows all) ❌
- Language: unlimited (shows all) ❌
- Location: unlimited (shows all) ❌
- Publisher: unlimited (shows all) ❌
- File Format: unlimited (shows all) ❌
- Keyword: unlimited (shows all) ❌
- Contributing Library: unlimited (shows all) ❌

**Production Comparison**  
On https://digitalhistory.lib.wvu.edu/catalog?locale=en, the same 3 facets are limited (Creator, Subject, Collection Type). So locally matches production — but indicates facet limiting was never fully implemented in catalog view.

**Code Context**

Yesterday we added:
- `CatalogControllerDecorator` — sets `facet_config.limit = 5` for ALL facets in a loop
- `CatalogSearchBuilderWrapper` — enforces Solr-level facet.limit params
- Initializer `999_catalog_controller_decorator.rb` — applies decorator via after_initialize hook

But the decorator's config loop is apparently not taking effect for non-limited facets.

---

## Task Objectives

**Primary Goal**: Understand the architectural difference between homepage and catalog facet rendering

**Deliverables**:
1. ✅ Identify which controller handles homepage facets (likely `Hyrax::HomepageController`)
2. ✅ Identify which controller handles catalog facets (likely `CatalogController` but verify)
3. ✅ Determine if they use different search builders or facet configs
4. ✅ Find where the facet config discrepancy originates
5. ✅ Identify if decorator is applied to both or only one
6. ✅ Recommend fix approach (extend decorator to catalog, or separate config, etc.)

---

## Investigation Approach

### Step 1: Trace Controller Usage
Check which controllers render the two views:

```bash
# Homepage controller
grep -r "def index" app/controllers/hyrax/homepage* 2>/dev/null | head -5

# Catalog controller
grep -r "def index" app/controllers/catalog* 2>/dev/null | head -5
```

Note: If not found in app/controllers, check hyrax-webapp submodule.

### Step 2: Identify Search Builders
Determine what search builder each controller uses:

```bash
# Look for search_builder_class references
grep -r "search_builder_class" app/controllers/ hyrax-webapp/app/controllers/ 2>/dev/null | grep -E "homepage|catalog"

# Look for default search builder
grep -r "def search_builder" app/controllers/ hyrax-webapp/app/controllers/ 2>/dev/null
```

### Step 3: Compare Facet Configurations

**Homepage facet config**:
- Check: `hyrax-webapp/app/controllers/hyrax/homepages_controller.rb` or equivalent
- Does it have `configure_blacklight` block?
- Does it set facet limits?
- Does it reference `HomepageSearchBuilderWrapper`?

**Catalog facet config**:
- Check: `app/controllers/catalog_controller_decorator.rb` (our code)
- Verify `configure_blacklight` block is being called
- Verify loop setting `facet_config.limit = 5` for all fields

### Step 4: Test Decorator Application
Determine if decorator is actually applied:

```bash
# Check if prepend is being called for CatalogController
grep -r "CatalogControllerDecorator" app/ config/initializers/ 2>/dev/null

# Check initializer timing
cat config/initializers/999_catalog_controller_decorator.rb
```

### Step 5: Analyze Why Only 3 Facets Limit

**Hypothesis A**: Only 3 facets exist in the decorator's config loop  
**Hypothesis B**: Config is applied but facet_fields hash is incomplete  
**Hypothesis C**: Different code path used for non-limited facets (Solr default limits)

Test by checking:
- What facet_fields exist in blacklight_config for CatalogController
- Are all visible facets in that config?
- Are any facets hard-coded to specific limits elsewhere?

---

## Acceptance Criteria

- [ ] Identified which controller/view renders homepage facets
- [ ] Identified which controller/view renders catalog facets
- [ ] Traced why homepage limits all facets but catalog only limits 3
- [ ] Verified if CatalogControllerDecorator is applied to catalog index action
- [ ] Determined if facet fields are complete in catalog config
- [ ] Found root cause (incomplete config, different code path, or decorator not applied)
- [ ] Synthesis report saved at: `projects/wvulibraries_knapsack/summaries/2026-08-26-INVESTIGATION-FACET-CONFIG-DISCREPANCY.md`
- [ ] Recommended fix approach provided (extend config, patch decorator, or other)
- [ ] Task file moved to completed/ with status: completed
- [ ] Commit created and pushed to origin/main

---

## Architecture Context

**Homepage Search Builder** (working correctly):
- Uses: `HomepageSearchBuilderWrapper` 
- Sets facet limits in search builder (Solr-level params)
- Result: ALL facets limited consistently ✅

**Catalog Search Builder** (partially working):
- Uses: `CatalogSearchBuilderWrapper` (should be applied via decorator)
- Sets facet limits in search builder (Solr-level params)
- Result: ONLY 3 facets limited (Creator, Subject, Collection Type) ❌
- Others: unlimited (use Solr defaults or unset config)

**Key Question**: Why does homepage config succeed uniformly but catalog config only affects 3 facets?

---

## Gotchas & Notes

**⚠️ Submodule Context**:
- Homepage controller likely in `hyrax-webapp/app/controllers/hyrax/homepages_controller.rb`
- CatalogController in `app/controllers/catalog_controller.rb` (but may inherit from Hyku or Blacklight)
- Our decorator is `app/controllers/catalog_controller_decorator.rb`

**⚠️ Configuration Timing**:
- Decorator applied via `after_initialize` hook (late in boot sequence)
- `configure_blacklight` block might run before decorator is applied
- May need to verify decorator actually modifies the config

**⚠️ Solr Defaults**:
- Solr default facet.limit is unlimited (returns all values)
- If only 3 facets show limit params → others may be falling through to Solr defaults
- This would explain why 3 facets limit (have our params) and others don't (using Solr default)

**⚠️ Search Builder Execution**:
- Verify `CatalogSearchBuilderWrapper.build()` is actually called for catalog searches
- Check if it's outputting the facet.limit params for ALL facets or just 3

---

## Related Work

**Earlier Investigation** (2026-08-25):
- Identified that `params[:limit]` controls pagination, not facet display
- Determined that Blacklight reads `facet_config.limit` for display counts
- Found pagination "More" links depend on facet counts from Solr response

**Current Issue**:
- Decorator sets `facet_config.limit = 5` for ALL fields in loop
- But only 3 fields actually show limited counts in UI
- Root cause unknown → this investigation

---

## References

- **CatalogControllerDecorator**: app/controllers/catalog_controller_decorator.rb (lines ~27-45, the config loop)
- **CatalogSearchBuilderWrapper**: app/search_builders/catalog_search_builder_wrapper.rb
- **Initializer**: config/initializers/999_catalog_controller_decorator.rb
- **Homepage**: hyrax-webapp/app/controllers/hyrax/homepages_controller.rb (for comparison)
- **Status**: projects/wvulibraries_knapsack/status.md
