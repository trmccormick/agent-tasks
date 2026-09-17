---
status: active
priority: CRITICAL
type: architecture
system_domain: M3_METADATA, FACETING, SEARCH
mvp_alignment: PRODUCTION_READINESS
local_worker_safe: true
requires_tenant_build: true
tags: [facet-limiting, m3-flexible-metadata, scalability, upstream-ready, demo-vs-production]
created: 2026-09-16
updated: 2026-09-16
discovered_by: conversation analysis (demo vs. production data)
blocked_by: none
blocking: [production-deployment, hyku-upstream-pr]
solution_branch: TBD (will refactor fix/hide-type-facet-add-show-more-facets)
---

# CRITICAL: Design Proper Facet Limiting Solution (Not Band-Aid)

## 🚨 PROBLEM STATEMENT

**Current solution works on demo but FAILS on production:**

| Environment | Data | Facets Visible | Status | Why |
|---|---|---|---|---|
| demo-hykudev.lib.wvu.edu | Small test sample | ~3 facets | ✅ Works | Band-aid hardcodes exactly these 3 |
| digitalhistory.lib.wvu.edu | Production | ~15+ facets | ❌ FAILS | Band-aid only patches 3; others truncate silently |

**Root Issue**: `config/initializers/catalog_controller_decorator.rb` contains:
```ruby
# BAND-AID: Force-registers only 3 hardcoded facets
critical_facets = {
  'date_created_sim' => 'Date Created',
  'location_sim' => 'Location',
  'people_represented_sim' => 'People Represented'
}
```

**Why this is wrong**:
1. ❌ Only 3 facets get `limit: 5, show_more: true`
2. ❌ Other M3 facets in profile get registered but NOT limited
3. ❌ When CatalogSearchBuilder sets `f.<field>.facet.limit`, it only works for registered facets with explicit `limit: 5`
4. ❌ Unregistered facets get Solr default (facet.limit=5) with no "more" link detection (need limit+1)
5. ❌ Adding new M3 fields requires code change + deployment

**Consequence**: Users see truncated facet lists on production (silently broken UX).

---

## CURRENT ARCHITECTURE (BAND-AID)

**Works Because**:
- M3 dynamic discovery finds all properties with _sim fields ✅
- Force-registration ensures 3 critical ones have `limit: 5` (integer) ✅
- CatalogSearchBuilder detects `limit: 5` config and sets `f.<field>.facet.limit = 6` ✅
- Blacklight detects 6 items returned (vs 5 configured) and shows "more" link ✅

**Fails Because**:
- If M3 profile has 20 facets and we only force-register 3:
  - 3 get `limit: 5, show_more: true` (work correctly)
  - 17 get `limit: nil` (Blacklight shows all items, Solr default is 5, no "more" link)
  - Result: Some facets show "more", others show truncated list silently

---

## INVESTIGATION: WHY DOES HOMEPAGE WORK?

**Key Clue**: HomepageController (which inherits from CatalogController) works WITHOUT our patches:

**File**: `hyrax-webapp/app/controllers/catalog_controller.rb` lines 127-138
```ruby
# Base Hyku config has hardcoded facets with limit: 5
config.add_facet_field 'creator_sim', label: I18n.t('blacklight.search.fields.facet.creator'), limit: 5
config.add_facet_field 'subject_sim', label: I18n.t('blacklight.search.fields.facet.subject'), limit: 5
# ... 4 more hardcoded facets, all limit: 5
```

**Question**: Why do homepages facets show "more" without our patches?

**Hypothesis to investigate**:
1. Does HomepageController override `add_facetting_to_solr` somewhere?
2. Is there system-wide initialization that registers M3 facets at boot time?
3. Does `hyrax-webapp/config/initializers/` have a FlexibleSchema initializer?
4. Are M3 facets being registered in `Hyku::Engine` or tenant initialization?
5. Is HomepageSearchBuilderWrapper doing something that CatalogSearchBuilder isn't?

**Investigation Resources**:
- `hyrax-webapp/app/controllers/homepage_controller.rb` (check for overrides)
- `hyrax-webapp/config/initializers/` (look for facet/schema initialization)
- `hyrax-webapp/lib/hyku/engine.rb` (check engine initialization order)
- Git history of Hyku for FlexibleSchema integration

---

## ACCEPTANCE CRITERIA

✅ **Must satisfy ALL of these**:

### 1. Dynamic Discovery (No Hardcoding)
- [ ] ALL M3 facets automatically get `limit: 5` config
- [ ] No hardcoded field names in decorator
- [ ] Adding new M3 field to profile works without code change

### 2. Scalability
- [ ] Works with 3 facets (demo)
- [ ] Works with 15+ facets (production)
- [ ] Works with any number of facets user adds to M3 profile
- [ ] Works with custom M3 profiles from different tenants

### 3. Upstream-Ready
- [ ] Solution can be backported to Hyku (not WVU-specific)
- [ ] Or solution can be backported to Hyrax (upstream of Hyku)
- [ ] No hard dependencies on WVU infrastructure
- [ ] Documented so upstream reviewers understand

### 4. Proper Architecture
- [ ] Understand WHERE fix belongs (Hyrax, Hyku, or both)
- [ ] Justify why band-aid (decorator) is wrong approach
- [ ] Propose proper location (engine initialization? Hyku tenant setup? Hyrax core?)

### 5. Backward Compatibility
- [ ] Works with pre-M3 metadata systems (non-flexible)
- [ ] Works with existing hardcoded facets (creator_sim, subject_sim, etc.)
- [ ] Doesn't break when both M3 and traditional facets are present

### 6. Testing & Validation
- [ ] Test on demo-hykudev with 3 facets ✅
- [ ] Test on digitalhistory with 15+ facets
- [ ] Verify CatalogSearchBuilder logs show ALL facets getting limit+1
- [ ] Verify Solr receives limit+1 for all facets

---

## INVESTIGATION PLAN

### Phase 1: Understand Homepage Mechanism (Highest Priority)
**Goal**: Find how HomepageController shows "more" links without patches

```
1. Check hyrax-webapp/app/controllers/homepage_controller.rb
   - Does it override configure_blacklight?
   - Does it override search_builder_class?
   - Does it have custom facet configuration?

2. Check if there's system-wide M3 facet registration
   - hyrax-webapp/config/initializers/*.rb (look for facet/flexible/metadata)
   - hyrax-webapp/lib/hyku/engine.rb (check engine setup)
   - Does engine auto-register M3 facets somewhere?

3. Check if HomepageSearchBuilderWrapper is doing work
   - Compare with CatalogSearchBuilder
   - Does it set facet.limit differently?
   - Does it use a different facet discovery method?

4. Check Hyku's FlexibleSchema initialization
   - Is there a system-wide hook that initializes facets?
   - When/where does Hyku load active M3 profile?
   - Is initialization happening at app boot vs. per-request?
```

**Success Criteria**: Identify the EXACT mechanism that makes homepage work without patches.

### Phase 2: Determine Architectural Home
**Goal**: Decide WHERE the fix properly belongs

| Option | Pros | Cons | Verdict |
|---|---|---|---|
| **Option A: Hyrax core** | Upstream for all | Changes community code, big PR | Investigate if possible |
| **Option B: Hyku tenant init** | Tenant-aware, works for flex metadata | Only for Hyku, not pure Hyrax | Likely home |
| **Option C: Search builder level** | Automatic for all searches | May not be discoverable | Possible complement |
| **Option D: Engine initializer** | Runs once at boot, clean | May not handle dynamic profile changes | Investigate |

**Success Criteria**: Clear decision on WHERE fix belongs + justification.

### Phase 3: Design Upstream-Ready Solution
**Goal**: Create fix that's backportable (not WVU-specific)

**Requirements**:
- [ ] No hardcoded field names
- [ ] Works with ANY M3 profile (not just WVU's)
- [ ] Works with Hyku and/or Hyrax without modification
- [ ] Doesn't require WVU-specific infrastructure

**Deliverables**:
- [ ] Refactored `catalog_controller_decorator.rb` (remove hardcoded facets section)
- [ ] New file in proper location (TBD based on Phase 2)
- [ ] Comments explaining why this approach is correct
- [ ] Commit message suitable for upstream PR

### Phase 4: Validate Without Band-Aid
**Goal**: Test that proper solution works without hardcoded facets

**Testing**:
- [ ] Remove hardcoded 3-facet section from decorator
- [ ] Test on demo-hykudev (should still work via proper mechanism)
- [ ] Test on digitalhistory with production data (should handle all facets)
- [ ] Verify logs show ALL facets with limit: 5 and limit+1 params

**Success**: All "more" links work without any hardcoded field names.

---

## KEY QUESTIONS TO ANSWER

Before implementation, clarify:

1. **Homepage Mechanism**: How does homepage work without patches? (MUST investigate first)
2. **Architectural Home**: Is fix in Hyrax, Hyku, or both? Why not decorator?
3. **M3 Registration Timing**: 
   - When should facets be registered? (app boot, per-tenant, per-request?)
   - Should it happen when M3 profile is uploaded? (dynamic)
4. **Backward Compatibility**: 
   - How to handle legacy systems without M3?
   - What if site has mix of M3 and non-M3 work types?
5. **Upstream Communication**: 
   - Has Hyku/Hyrax community encountered this issue?
   - Is there existing pattern for facet auto-discovery?

---

## NOTES & CONTEXT

**Related Files**:
- Band-aid code: `config/initializers/catalog_controller_decorator.rb` (lines 107-130)
- Search builder: `app/search_builders/catalog_search_builder.rb`
- Facet enforcement: `config/initializers/facet_limits.rb`
- Working pattern: `app/search_builders/hyrax/homepage_search_builder_wrapper.rb` (why does it work?)
- Base config: `hyrax-webapp/app/controllers/catalog_controller.rb` (hardcoded facets)

**Related Sessions**:
- 2026-09-14: Grok validated band-aid works on demo
- 2026-09-11: Branch merged with dynamic discovery + force-registration
- 2026-08-25: Qwen tested facet limiting on filtered results

**Demo vs Production Reality**:
- Demo test uses small sample data → only 3 facets visible
- Production (digitalhistory) has full M3 profile → many more facets
- Solution that "works" on demo reveals its limitations on real data

---

## BLOCKERS & DEPENDENCIES

🔴 **CRITICAL**: Must answer homepage question before proceeding
- If homepage has special code → we can replicate it for catalog
- If homepage uses system-wide initialization → we should use same approach
- If homepage has nothing special → search deeper (initialization hooks, engines, etc.)

---

## SUCCESS DEFINITION

**Done when**:
- [ ] Homepage mechanism fully understood and documented
- [ ] Architectural decision made (Hyrax/Hyku/both + file location)
- [ ] Proper fix implemented without ANY hardcoded facet field names
- [ ] Demo-hykudev still works with proper mechanism (not band-aid)
- [ ] Test on production data (15+ facets) shows all facets with "more" links
- [ ] Band-aid comments removed from code
- [ ] Solution is ready for upstream PR to Hyku or Hyrax
