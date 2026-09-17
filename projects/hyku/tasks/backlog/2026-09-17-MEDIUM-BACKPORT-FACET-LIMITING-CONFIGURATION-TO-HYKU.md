---
status: backlog
priority: MEDIUM
type: feature-backport
system_domain: FACETING, SEARCH, CONFIGURATION, UPSTREAM
mvp_alignment: UPSTREAM_CONTRIBUTION
local_worker_safe: true
requires_tenant_build: false
tags: [facet-limiting, backport, hyku-upstream, configuration]
created: 2026-09-17
updated: 2026-09-17
discovery_context: WVU Knapsack YAML-driven facet limiting verified working on demo-hykudev; ready for upstream backport after production validation
related_projects: [wvulibraries_knapsack]
blocking_issue: None - WVU production deployment must be verified first
---

## Agent Dispatch Interface

**Role**: Implementation & Testing Agent  
**Project**: Hyku (samvera/hyku upstream backport)  
**Task**: Backport WVU's facet limiting solution to Hyku main with tests

**Step 0**: Do NOT start until WVU production (digitalhistory.lib.wvu.edu) validates the fix. Wait for handoff from Tracy with production validation.

**Key Context**: 
- WVU Knapsack solution is production-ready and verified on demo-hykudev
- Generic components (CatalogSearchBuilder + M3 discovery) are ready for upstream
- This task creates feature branch, adapts code for Hyku, tests locally
- Blocking dependency: Production validation from WVU ops

---

## 🚀 BACKPORT: Facet Limiting Configuration to Hyku Main

### Background

**What WVU Built** (Verified Working):
1. `CatalogSearchBuilder` — Extends AdvSearchBuilder, enforces facet.limit = limit+1 for all facets
2. `catalog_controller_decorator.rb` — Dynamic M3 discovery + YAML configuration
3. `wvu_facet_defaults.yml` — Configuration file for force-registered fields
4. Docker-safe path resolution using `__FILE__`

**Why It's Valuable for Hyku**:
- Solves facet "more" link detection for ALL facets (not per-query)
- Works with FlexibleSchema M3 metadata profiles
- Configuration-driven (no code changes needed per tenant)
- Generic pattern suitable for any Hyku instance

**Current State**:
- ✅ Deployed to demo-hykudev.lib.wvu.edu
- ✅ Verified working (logs, facet counts, Solr params)
- ✅ Ready for production (digitalhistory.lib.wvu.edu)
- ⏳ **Blocked**: Wait for production validation before backporting

---

## 📋 Backport Workflow

### Phase 1: Prepare (After Production Validation)

**Prerequisites**:
- [ ] WVU production (digitalhistory.lib.wvu.edu) deployed and validated
- [ ] Handoff from Tracy with production validation results
- [ ] Access to wvu_knapsack repo to extract code
- [ ] Hyku main branch ready (typically does NOT need hyrax-webapp submodule for new features)

**Step 1: Create Feature Branch**
```bash
cd ~/Documents/git/hyku
git checkout main
git pull origin main
git checkout -b feature/facet-limiting-configuration
```

**Step 2: Copy and Adapt CatalogSearchBuilder**

Source: `wvu_knapsack/app/search_builders/catalog_search_builder.rb`

Adapt for Hyku:
- Remove WVU-specific logging if desired
- Ensure inherits from Hyku's AdvSearchBuilder (may be in hyrax-webapp gem)
- Add comprehensive comments for upstream audience
- Create spec file at `spec/search_builders/catalog_search_builder_spec.rb`

**Step 3: Create Generic Facet Configuration Decorator**

Source: `wvu_knapsack/config/initializers/catalog_controller_decorator.rb`

Adapt for Hyku:
- Rename to `config/initializers/hyku_facet_configuration.rb` (more generic)
- Remove WVU-specific field names (date_created_sim, location_sim, etc.)
- Keep M3 discovery pattern (generic for any FlexibleSchema)
- Make YAML path configurable via Hyku config or environment
- Add specs for each section (M3 discovery, YAML loading, limit application)

**Step 4: Create Default Configuration File**

```yaml
# config/facet_defaults.yml.example
defaults:
  limit: 5
  show_more: true

# Tenants can customize this per tenant:
# config/tenants/<tenant-id>/facet_defaults.yml
force_registered_fields: {}
```

**Step 5: Add Tests**

Create test files:
- `spec/search_builders/catalog_search_builder_spec.rb`
  - Test facet.limit = limit+1 is set
  - Test all facets in config are processed
  
- `spec/initializers/hyku_facet_configuration_spec.rb`
  - Test M3 discovery from FlexibleSchema
  - Test YAML loading (when exists/missing)
  - Test force-registered fields
  - Test universal limit application

**Step 6: Document Configuration**

Create `doc/facet_limiting_configuration.md`:
- Explain why facet limiting matters (Blacklight detection)
- Show how limit+1 works at Solr level
- Document custom YAML configuration pattern
- Provide examples for tenant customization

---

### Phase 2: Test Locally

**Step 1: Run Tests**
```bash
bundle exec rspec spec/search_builders/catalog_search_builder_spec.rb
bundle exec rspec spec/initializers/hyku_facet_configuration_spec.rb
```

**Step 2: Integration Test (Local Dev)**
```bash
docker compose up
# Visit http://localhost:3000/catalog?search_field=all_fields&q=
# Verify:
# - Facets display
# - Facets with >5 results show "more" link
# - All facets configured have limit: 5
```

**Step 3: Check Logs**
```bash
docker compose exec web tail -f log/development.log | grep -E "facet|YAML"
```

---

### Phase 3: Submit PR

**PR Description Template**:
```markdown
## Feature: Global Facet Limiting Configuration for Hyku

### Problem
- Blacklight facets need Solr-level limit enforcement to detect "more" links
- Current Hyku only has per-query facet.limit (in specific presenters)
- No global, generic approach for all facets

### Solution
- New `CatalogSearchBuilder` enforces facet.limit = limit+1 globally
- New facet configuration decorator supports:
  - Dynamic M3 FlexibleSchema facet discovery
  - YAML-driven configuration for force-registered fields
  - Universal limit application to all _sim facets

### Benefits
- "More" links now work for all facets (not just hardcoded ones)
- Scales to any number of facets (no code changes)
- Configuration-driven (tenant-specific YAML files)
- Compatible with FlexibleSchema M3 metadata profiles

### Testing
- Unit tests for CatalogSearchBuilder
- Integration tests for facet configuration
- Local dev validation
- (Future: smoke test on staging Hyku)

### References
- Based on WVU Knapsack implementation
- Verified on demo-hykudev.lib.wvu.edu (production equivalent)
- Generic pattern suitable for all Hyku instances
```

**Branch Ready for PR**:
- Naming: `feature/facet-limiting-configuration`
- Base: `main`
- No merge conflicts expected (new files only)

---

## 📝 Acceptance Criteria

- [ ] CatalogSearchBuilder implemented and tested
- [ ] Hyku facet configuration decorator implemented and tested
- [ ] Default YAML configuration file created
- [ ] All tests passing locally
- [ ] Documentation added (doc/facet_limiting_configuration.md)
- [ ] PR description complete with examples
- [ ] Ready for community review
- [ ] Can be merged without impacting existing Hyku instances

---

## 🔧 Key Files

**Source (WVU Knapsack)**:
- `wvu_knapsack/app/search_builders/catalog_search_builder.rb`
- `wvu_knapsack/config/initializers/catalog_controller_decorator.rb`
- `wvu_knapsack/config/wvu_facet_defaults.yml`

**Target (Hyku Main)**:
- `app/search_builders/catalog_search_builder.rb` (new)
- `config/initializers/hyku_facet_configuration.rb` (new, adapted from decorator)
- `config/facet_defaults.yml.example` (new)
- `spec/search_builders/catalog_search_builder_spec.rb` (new)
- `spec/initializers/hyku_facet_configuration_spec.rb` (new)
- `doc/facet_limiting_configuration.md` (new)

---

## ⚠️ Blocking Dependency

**DO NOT START** until:
- [ ] WVU production (digitalhistory.lib.wvu.edu) validation complete
- [ ] Handoff received from Tracy with go/no-go decision
- [ ] Any production issues resolved

This ensures the solution is proven on real production data before contributing upstream.

---

## 💡 Notes for Implementation Agent

**Hyku-Specific Considerations**:
1. AdvSearchBuilder might be in hyrax-webapp gem (check inheritance chain)
2. Hyku may have tenant-specific config loading (check Hyku::Tenant)
3. M3 FlexibleSchema may have tenant awareness (use current_version if available)
4. Default facet limits may already be set in Hyku core (don't duplicate)
5. Testing may need to account for multi-tenant initialization

**Upstream Review Expectations**:
- Maintainers will ask about backward compatibility
- May need feature flag for gradual rollout
- Could propose moving to Hyrax core if high value
- Expect suggestions for configuration location/strategy

**Testing Strategy**:
- Unit tests cover each component independently
- Integration tests verify full flow (M3 discovery → YAML → Solr params)
- Manual testing on dev VM confirms UI behavior
- Consider edge cases (no FlexibleSchema, no YAML file, empty M3 profile)

