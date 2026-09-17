---
status: backlog
priority: CRITICAL
type: investigation
system_domain: M3_METADATA, FACETING, ARCHITECTURE
mvp_alignment: UPSTREAM_ARCHITECTURE
local_worker_safe: true
requires_tenant_build: false
tags: [facet-limiting, homepage, catalog, m3-discovery, architecture-investigation]
created: 2026-09-16
---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Research Agent**.

Project: wvulibraries_knapsack
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/active/2026-09-16-RESEARCH-HOMEPAGE-FACET-INVESTIGATION.md

STEP 0 — MOVE TASK FILE TO ACTIVE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvulibraries_knapsack/tasks/backlog/2026-09-16-RESEARCH-HOMEPAGE-FACET-INVESTIGATION.md \
         projects/wvulibraries_knapsack/tasks/active/2026-09-16-RESEARCH-HOMEPAGE-FACET-INVESTIGATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

READ FIRST (after Step 0): Task file contains all research questions, investigation plan, and synthesis requirements.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE finishing.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/
  Filename pattern: RESEARCH-HOMEPAGE-FACET-MECHANISM.md
  Chat is for questions only — synthesis report goes to file, not chat.
```

---

# RESEARCH TASK: Why Do Homepage Facets Work Without Patches?

## 🎯 INVESTIGATION OBJECTIVE

**The Key Question**: CatalogController facets need our patches to show "more" links, but HomepageController facets work WITHOUT any patches. Why?

**Why This Matters**: Finding this mechanism is the KEY to understanding the PROPER way to implement facet limiting. Our current solution is a band-aid (hardcoded 3 facet names). The answer to this question will show us the RIGHT architectural approach.

---

## BACKGROUND CONTEXT

**Demo Environment Reality**:
- demo-hykudev.lib.wvu.edu has small test dataset with ~3 facets
- Current solution hardcodes these 3 facet names and force-registers them
- This appears to "work" perfectly on demo (lucky coincidence)

**Production Reality**:
- digitalhistory.lib.wvu.edu has full M3 profile with 15+ facets
- If we deploy current solution: only 3 facets work, other 12+ silently fail
- PROOF: HomepageController (inherits from CatalogController) works WITHOUT patches
  - So the mechanism MUST be elsewhere (not in our decorators)

**The Puzzle to Solve**:
```
CatalogController (uses our patches):
  ✅ 3 hardcoded facets show "more" links (because we force-register them)
  ❌ Other facets truncate silently (because we don't patch them)

HomepageController (NO patches at all):
  ✅ All facets show "more" links correctly
  ❓ How is this possible without our code?

Hypothesis: HomepageController has access to proper facet initialization 
that CatalogController is missing. We need to find and replicate it.
```

---

## RESEARCH PLAN

### Phase 1: Inspect Homepage Controller Code
**Goal**: Understand how HomepageController is configured

**Files to Review**:
1. `hyrax-webapp/app/controllers/homepage_controller.rb`
   - Does it override `configure_blacklight`?
   - Does it set `search_builder_class`?
   - Does it register facets explicitly?
   - Does it call parent's configure_blacklight?

2. `hyrax-webapp/app/controllers/catalog_controller.rb` (base class)
   - Line 127-138: Hardcoded facet config (creator_sim, subject_sim, etc.)
   - Search for `configure_blacklight` block
   - Look for M3 or FlexibleSchema references

**Questions to Answer**:
- [ ] Does HomePage inherit Catalog's facet config or override it?
- [ ] Are there different search builders for Homepage vs Catalog?
- [ ] Any special setup in HomepageController's `configure_blacklight`?

---

### Phase 2: Search for System-Wide M3 Initialization
**Goal**: Find if there's initialization code that registers M3 facets everywhere

**Files to Check**:
1. `hyrax-webapp/config/initializers/*.rb` (ALL files)
   - Look for: FlexibleSchema, facet, M3, metadata, profile
   - Any `configure_blacklight` blocks?
   - Any facet registration logic?
   - Search term: `add_facet_field`

2. `hyrax-webapp/lib/hyku/engine.rb`
   - Does engine initialize facets?
   - Any `to_prepare` hooks?
   - Any initialization of FlexibleSchema?
   - Look for: `config.`, `initializer`, `ActiveSupport`

3. `hyrax-webapp/lib/hyku.rb`
   - Hyku module setup
   - Any facet-related configuration?

**Questions to Answer**:
- [ ] Is there a boot-time facet initialization we're missing?
- [ ] Does Hyku engine auto-register M3 facets somewhere?
- [ ] Any system-wide FlexibleSchema initialization?

---

### Phase 3: Investigate Search Builders
**Goal**: Understand if different search builders handle facets differently

**Files to Review**:
1. `hyrax-webapp/app/search_builders/hyrax/homepage_search_builder.rb`
   - Does it override `add_facetting_to_solr`?
   - Does it handle facets differently than Catalog?
   - Compare method signature and behavior

2. `app/search_builders/hyrax/homepage_search_builder_wrapper.rb` (OUR CODE)
   - Why does this exist? What does it do?
   - Does it inject facet limits?
   - How does it differ from what CatalogSearchBuilder does?

3. `hyrax-webapp/app/search_builders/catalog_search_builder.rb` (or similar base)
   - Look for facet-related methods
   - Compare with homepage builder

**Questions to Answer**:
- [ ] Do homepage and catalog search builders inherit from same base?
- [ ] Is there a difference in how they call `add_facetting_to_solr`?
- [ ] Does HomepageSearchBuilderWrapper do something special?

---

### Phase 4: Check FlexibleSchema Registration Points
**Goal**: Find where/when FlexibleSchema facets get registered

**Search Strategy**:
1. Search entire hyrax-webapp for:
   - `FlexibleSchema.current_version`
   - `configure_blacklight` + facet
   - `add_facet_field` (especially in loops/conditionals)
   - `flexible?` (checks if M3 enabled)

2. Look for dynamic facet registration (not hardcoded):
   - Any code that iterates properties?
   - Any code that discovers _sim fields?
   - Any initialization that happens per-tenant?

3. Check if there's a difference between:
   - Boot-time initialization (app startup)
   - Request-time initialization (per-request hooks)
   - Tenant-specific initialization (Hyku multitenant)

**Files to Search**:
- All of `hyrax-webapp/config/initializers/`
- All of `hyrax-webapp/lib/hyku/`
- All of `hyrax-webapp/app/controllers/`
- All of `hyrax-webapp/app/search_builders/`

---

### Phase 5: Compare With Our Patches
**Goal**: Identify what we're doing differently

**Files We Modified**:
- `config/initializers/catalog_controller_decorator.rb` → force-registers 3 facets
- `app/search_builders/catalog_search_builder.rb` → sets facet.limit = limit+1
- `config/initializers/facet_limits.rb` → enforces limit: 5

**Questions**:
- [ ] Are these patches doing things that Hyku SHOULD be doing automatically?
- [ ] Why doesn't CatalogController get M3 facets registered like HomePage does?
- [ ] Is there a missing initialization step in our bootstrap?

---

## RESEARCH DELIVERABLES

Create a synthesis report file: `summaries/RESEARCH-HOMEPAGE-FACET-MECHANISM.md`

Include:

### 1. Findings Summary
- Where DO homepage facets come from?
- Is there system-wide initialization we're missing?
- How does Hyku normally handle M3 facet registration?

### 2. Code References
- Link to exact files/lines showing how it works
- Explain the flow (boot → initialization → configure_blacklight → facet discovery)

### 3. The Comparison
```
Homepage Facets (works without patches):
  Initialization: [describe the mechanism]
  Location: [which file/which hook]
  Scope: [app-wide, per-tenant, per-request]

Catalog Facets (broken without patches):
  Why different: [explain the gap]
  What's missing: [what should happen but doesn't]
  Why our band-aid works: [explain why hardcoding 3 facets masks the real issue]
```

### 4. Architecture Implications
- Where SHOULD facet registration live? (Hyrax, Hyku, Knapsack?)
- Why is decorator approach wrong?
- What's the proper approach?

### 5. Next Steps Recommendation
- What should the morning session do based on findings?
- Should we modify our approach based on what you discover?
- Any risks or blockers identified?

---

## RESEARCH HINTS & GOTCHAS

**Look for These Patterns**:
- ✅ Any `to_prepare` blocks (run after class definitions, good for monkey-patching)
- ✅ Any `config.add_facet_field` in initializers or engines
- ✅ Any code that checks `Hyrax.config.flexible?`
- ✅ Any code that uses `FlexibleSchema.current_version` or similar
- ✅ Any code in `lib/hyku/` (this is where Hyku-specific setup lives)

**Don't Get Lost In**:
- ❌ Blacklight gem internals (we're looking at app usage, not gem code)
- ❌ Hyrax gem internals (we're looking at hyrax-webapp app layer)
- ❌ Search results rendering (we care about config, not display)

**Key Insight**:
- The answer is probably in one of: hyrax-webapp initializers, Hyku engine setup, or base CatalogController
- It's probably doing something at boot time (not request time) since HomepageController works immediately
- It's probably using FlexibleSchema API (so look for imports/requires of that class)

---

## SUCCESS CRITERIA

You've found the answer when you can explain:

✅ "Homepage facets work because [specific mechanism in hyrax-webapp]"
✅ "This mechanism [does/doesn't] apply to CatalogController"
✅ "The proper fix should be [location in code]"
✅ "Our band-aid works because [explains why 3 hardcoded facets masks the real issue]"

---

## RESEARCH CONTEXT

- **Conversation Date**: 2026-09-16 (evening)
- **Branch Under Review**: `fix/hide-type-facet-add-show-more-facets` (commit 6f44a3e)
- **Demo Status**: Works perfectly with 3 hardcoded facets
- **Production Issue**: Won't scale (only patches 3 facets, production has 15+)
- **Critical Path**: Homepage investigation → architectural decision → proper fix design
- **Morning Handoff**: Results go to `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/RESEARCH-HOMEPAGE-FACET-MECHANISM.md`

---

## RESOURCES

**Repository Paths**:
- Hyku Core: `/Users/tam0013/Documents/git/wvu_knapsack/hyrax-webapp/`
- Our Customizations: `/Users/tam0013/Documents/git/wvu_knapsack/`
- Agent Tasks Tracking: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/`

**Related Session Notes**:
- Previous investigation: `agent-tasks/projects/wvulibraries_knapsack/tasks/active/2026-08-26-HIGH-INVESTIGATION-HOMEPAGE-VS-CATALOG-FACET-CONFIG.md`
- Current work: `agent-tasks/projects/wvulibraries_knapsack/tasks/active/2026-09-16-CRITICAL-DESIGN-PROPER-FACET-LIMITING-SOLUTION.md`

**Key Code Files**:
- Our patches: `config/initializers/catalog_controller_decorator.rb` (lines 107-130)
- Our search builder: `app/search_builders/catalog_search_builder.rb`
- Base Hyku facet config: `hyrax-webapp/app/controllers/catalog_controller.rb` (lines 127-138)
- Reference (works without patches): `app/search_builders/hyrax/homepage_search_builder_wrapper.rb`

---

## TIME ESTIMATE

**2-3 hours** for thorough research:
- 30 min: Homepage controller inspection
- 45 min: Search initializers and engine code
- 45 min: Search builders comparison
- 30 min: FlexibleSchema search across codebase
- 15 min: Synthesis and report writing
