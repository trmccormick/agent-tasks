---
project: Hyku (samvera/hyku) — Upstream Backport Tasks
created: 2026-09-17
updated: 2026-09-17
status: planning-phase
---

# Hyku Upstream Backport — Project Tracking

**Project Scope**: Track upstream contributions and backports from WVU Knapsack to Hyku main  
**Related Project**: wvulibraries_knapsack (verification and source)  
**Worker**: Qwen (when dispatched)

---

## Current Status

### 🔄 In Progress: WVU Production Validation (Blocker)

**What**: Deploy facet limiting fix to production (digitalhistory.lib.wvu.edu)  
**Blocking**: Hyku backport task cannot start until this is verified  
**When Done**: Will trigger backport task handoff to Qwen  
**Owner**: Tracy (WVU operations)  

---

## Planned Tasks

### 📋 Backport: Facet Limiting Configuration

**Status**: BACKLOG (waiting for production validation)  
**File**: `tasks/backlog/2026-09-17-MEDIUM-BACKPORT-FACET-LIMITING-CONFIGURATION-TO-HYKU.md`  
**Worker**: Qwen  
**Priority**: MEDIUM  
**Estimated**: 4-6 hours (implementation + testing)

**What Will Be Done**:
1. Create feature branch from Hyku main
2. Adapt CatalogSearchBuilder from WVU Knapsack
3. Create generic facet configuration decorator
4. Add comprehensive specs for both components
5. Create documentation and example configuration
6. Submit PR to Hyku for community review

**Why This Matters**:
- Solves facet "more" link detection for all Hyku instances
- Works with FlexibleSchema M3 metadata profiles
- Configuration-driven (no hardcoding)
- Generic pattern suitable for upstream contribution

---

## Reference: WVU Solution Components

**Already Verified Working**:
- ✅ CatalogSearchBuilder (app/search_builders/catalog_search_builder.rb)
- ✅ Facet configuration decorator (config/initializers/catalog_controller_decorator.rb)
- ✅ YAML configuration file (config/wvu_facet_defaults.yml)
- ✅ Docker-safe path resolution technique

**Deployment Status**:
- ✅ demo-hykudev.lib.wvu.edu (verified working)
- ⏳ digitalhistory.lib.wvu.edu (production — pending validation)

---

## Handoff Points

### When Production Validation Complete

Tracy will provide:
1. ✅/❌ Go/no-go decision for production
2. Any issues discovered and their fixes
3. Updated YAML configuration for production facets
4. Confirmation all 3+ facets show "more" links

### Trigger for Qwen

Once production validated, Qwen will:
1. Read production validation results
2. Extract verified code from wvu_knapsack
3. Create backport feature branch
4. Implement and test locally
5. Prepare PR for Hyku upstream

---

## Success Criteria

- [ ] WVU production validation complete (digitalhistory working)
- [ ] Backport branch created and ready (qwen-created after validation)
- [ ] All tests passing locally
- [ ] Documentation complete
- [ ] PR submitted to Hyku main
- [ ] Hyku maintainers review code (may take 1-2 weeks)

---

## Notes

**Why Wait for Production Validation?**
- Ensures solution works on real production data (15+ facets)
- Confirms no issues with large M3 metadata profiles
- Validates production YAML configuration
- Reduces risk of pushing incomplete solution upstream

**Timeline**
- Now: Production deployment in progress
- After validation: Qwen backport (can be parallel with prod testing)
- 1-2 weeks: Hyku community review
- Later: Potential merge to main

