# Handoff: Facet Limiting Fix FINAL - Architecture Discovery (2026-09-14 Evening)

## Status: ✅ CODE READY FOR DEPLOYMENT
**Branch**: `fix/hide-type-facet-add-show-more-facets`  
**Latest Commit**: c24aa61 — REFINED: Read ONLY from Hyrax::FlexibleSchema  
**Previous**: 7de1a64, 8355691, a77137e, e42517c, df57d55

---

## Problem & Solution

### What Was Fixed
M3 flexible-metadata facets showing 100+ items on catalog page instead of 5, with no "more" links for pagination.

### Root Cause Discovery
- M3 facets created in Solr but NOT registered in Blacklight's `facet_fields` config
- Without Blacklight registration, facets showed uncontrolled results and no "more" links
- Fresh docker resets failed because facets weren't registering at all

### Architecture Breakthrough (2026-09-14)
**Critical discovery**: Metadata profiles are NOT stored on disk:
- **Original profile**: `config/metadata_profiles/m3_profile.yaml` (disk - bootstrap only)
- **Active profiles** (v.2, v.3, etc.): **PostgreSQL database** (uploaded via web UI, versioned)
- **Hyrax::FlexibleSchema**: Points to active profile in DB
- **Web UI**: Users can upload/download/switch profiles → stored in PostgreSQL

**Implication**: Previous approaches (YAML fallback) read stale disk files. Solution must read from PostgreSQL via Hyrax schema.

---

## Final Solution (Commit c24aa61)

### Approach: Single Source of Truth
**Read ONLY from Hyrax::FlexibleSchema.current_version**

```ruby
schema = Hyrax::FlexibleSchema.current_version
if schema.present?
  json_schema = schema.json_schema || {}
  properties = json_schema['properties'] || {}
  # Extract _sim fields and register as facets
end
```

### Why This Works
1. **Fresh boot**: Schema table empty → no facets (graceful, safe - catalog not used until schema ready)
2. **After migrations**: Schema populates → facets auto-register from ACTIVE profile in DB
3. **Profile changes**: User uploads v.2 → v.3 → Hyrax schema updates → facets auto-update (no code changes!)

### Why Previous Approaches Failed
- **YAML fallback** (commit 7de1a64): Read stale `config/metadata_profiles/m3_profile.yaml` (old profile), not active v.2/v.3
- **Hardcoding** (commit a77137e): Only worked for specific facets, not future-proof
- **Fresh docker reset**: Schema table empty, so YAML fallback became active (wrong profile version)

---

## Files Modified

| File | Changes | Commit |
|------|---------|--------|
| `config/initializers/catalog_controller_decorator.rb` | Single-source facet registration from Hyrax schema | c24aa61 |
| `config/initializers/facet_limits.rb` | Applies limit:5 to all facets dynamically | 9ba5cc5+ |
| `config/initializers/search_builder_facet_limits.rb` | Injects facet.limit=(limit+1) to Solr | e42517c |
| `app/search_builders/catalog_search_builder.rb` | Extends AdvSearchBuilder (not Blacklight::SearchBuilder) | 2e35fbf |
| `script/verify_facet_limits.rb` | Runtime verification script | 2795bec |

---

## Testing & Validation

### Expected Behavior
- Date Created: 5 items + "more" link
- Location: 5 items + "more" link  
- People Represented: items + "more" link (if 6+)
- Creator: items + "more" link (has many)
- Subject: items + "more" link (has many)
- Type facet: HIDDEN

### Profile Version Testing
User has locally downloaded v.2 and v.3 from prod VM:
- Can import different versions via web UI
- Test that facets update correctly when profile switches
- Validate facet registration adapts to profile changes

### Validation Commands
```bash
# Verify search builder
docker compose -f docker-compose.production.yml exec web \
  bundle exec rails runner 'puts CatalogController.blacklight_config.search_builder_class'
# Expected: CatalogSearchBuilder

# Check facet configuration
docker compose -f docker-compose.production.yml exec web \
  bundle exec rails runner 'CatalogController.blacklight_config.facet_fields.keys.sort'
# Should list all M3 facets from active schema
```

---

## Deployment for Qwen

### Quick Deploy
```bash
git pull origin fix/hide-type-facet-add-show-more-facets
docker compose -f docker-compose.production.yml down
docker compose -f docker-compose.production.yml up -d web
sleep 15
# Visit: https://demo-hykudev.lib.wvu.edu/catalog?search_field=all_fields&q=
```

### Verification Checklist
- [ ] Facets appear on catalog page
- [ ] Date Created: 5 items + "more" link
- [ ] Location: 5 items + "more" link
- [ ] Creator/Subject: many items + "more" link
- [ ] Type facet hidden
- [ ] No console errors
- [ ] Logs show facet registration: `docker compose logs web | grep facet`

---

## Key Insights

### Profile Versioning is Now Future-Proof
1. Hyrax tracks active profile in PostgreSQL
2. Code reads from Hyrax schema (not disk)
3. When user uploads v.3 → Hyrax schema updates → our code auto-detects
4. No hardcoding, no stale data, no code changes needed

### Fresh Boot Behavior
- On docker reset: Hyrax::FlexibleSchema table empty initially
- Facets don't register until schema populates (safe, graceful)
- Once migrations complete and profiles load: Facets auto-register
- This is fine because catalog isn't used until system ready

### Single vs Dual Source
- **Removed**: YAML fallback (commit c24aa61) - was reading wrong profile
- **Kept**: Hyrax schema only - always correct, always current
- **Cleaner**: Single source of truth prevents sync issues

---

## Next Steps

1. **Deploy to Demo**: Use deployment task (steps above)
2. **Verify**: Test facets on catalog page
3. **Test Profile Adaptation**: Optional - upload different profile versions
4. **Merge PR**: After demo validation, merge to main
5. **Mark Task Complete**: Close facet-limiting investigation task

---

## Session Notes

This session included:
- Root cause analysis
- Architecture discovery (profiles in PostgreSQL, not disk)
- Refinement from dual-source to single-source approach
- Code cleanup and optimization
- Task handoff documentation

Total investigation: ~5 sessions (08-20, 08-25, 09-11, 09-14 morning, 09-14 evening)
