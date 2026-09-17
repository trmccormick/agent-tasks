# Session Handoff — 2026-09-16 (Facet Scaling Realization)

**Context**: Catalog facet "more" links were implemented using band-aid approach (hardcoding 3 facets). Session recognized this won't scale to production.

**Branch**: `fix/hide-type-facet-add-show-more-facets` (commit 6f44a3e) — ✅ works on demo, ❌ won't scale to production

---

## CRITICAL DISCOVERY

**Demo-hykudev vs. digitalhistory reality:**

| Site | Data Size | Visible Facets | Current Fix | Will Work? |
|---|---|---|---|---|
| demo-hykudev | Small test sample | ~3 facets | Force-registers exactly 3 | ✅ Perfect fit |
| digitalhistory | Full production | ~15+ facets | Force-registers only 3 | ❌ Fails |

**Why demo works but production won't**:
- Current code hardcodes 3 facet names: `date_created_sim`, `location_sim`, `people_represented_sim`
- Demo dataset happens to have ONLY these 3 facets → appears to work perfectly
- Production M3 profile has 15+ facets → 12+ will silently truncate with no "more" link

**Band-aid location**: `config/initializers/catalog_controller_decorator.rb` lines 107-130

---

## WHAT'S WORKING (DO NOT BREAK)

- ✅ CatalogSearchBuilder correctly sets `f.<field>.facet.limit = 6` (limit+1)
- ✅ facet_limits.rb enforces `limit: 5` on all facets
- ✅ M3 dynamic discovery finds _sim fields and registers them
- ✅ Homepage facets work (without our patches) — mechanism to be investigated
- ✅ Blacklight shows "more" links when 6 items returned (vs 5 configured)

---

## NEXT IMMEDIATE WORK

**Priority 1: Investigate Homepage Mechanism** (highest-value question)
- Why do HomepageController facets work without patches?
- Check: `hyrax-webapp/app/controllers/homepage_controller.rb`
- Check: Is there system-wide M3 initialization in hyrax-webapp/config/initializers/ ?
- Check: Does Hyku engine have FlexibleSchema initialization hook?
- **Goal**: Find the RIGHT way to register facets (not the band-aid way)

**Priority 2: Architectural Decision**
- Should fix be in Hyrax, Hyku, or both?
- Is decorator the right approach? (probably not)
- Should facet registration happen at app boot, per-tenant, or per-request?

**Priority 3: Design Proper Solution**
- Remove hardcoded 3-facet section
- Implement proper mechanism (identified in Priority 1)
- Ensure it handles ANY M3 profile (not just WVU's)
- Works with 3 facets, 15+ facets, custom facets added dynamically

**Priority 4: Test & Validate**
- Test on demo-hykudev (should still work via proper mechanism)
- Test on digitalhistory with production data (all facets should work)
- Remove all hardcoded field names from code
- Create commit message suitable for upstream PR

---

## KEY FILES & BRANCHES

**Current band-aid solution**:
- `config/initializers/catalog_controller_decorator.rb` (force-registers 3 facets)
- `app/search_builders/catalog_search_builder.rb` (sets limit+1 params)
- `config/initializers/facet_limits.rb` (enforces limit: 5)

**Comparison: Homepage (works without patches)**:
- `app/search_builders/hyrax/homepage_search_builder_wrapper.rb` (different approach?)
- `hyrax-webapp/app/controllers/catalog_controller.rb` (base config with hardcoded facets)

**Tracking**:
- Active task: `tasks/active/2026-09-16-CRITICAL-DESIGN-PROPER-FACET-LIMITING-SOLUTION.md`
- Investigation checklist included in task

---

## FOR NEXT AGENT/SESSION

1. **Read the critical task file first**: It contains full investigation plan & questions
2. **Start with homepage investigation**: This is the KEY to understanding proper solution
3. **Do NOT remove the band-aid yet**: Leave it working until you understand homepage
4. **Test plan**: Run command `curl -s "https://demo-hykudev.lib.wvu.edu/catalog?search_field=all_fields&q=" | grep "more"` to verify facet links
5. **Production test**: Compare with `https://digitalhistory.lib.wvu.edu/catalog` to see how many facets exist

---

## CONVERSATION CONTEXT

**Session Goal**: Finish up properly; recognize that demo fix won't scale to production

**User Insight**: 
> "if you look at digitalhistory.lib.wvu.edu and see the facets there there are more than just 3 that most likely are not limited and will need the correct treatment. what we are seeing on demo-hykudev.lib.wvu.edu is a small test sample of data that is why this solution will not work."

**Outcome**: Proper realization that demo-specific solution won't scale; created comprehensive task for proper design phase.

---

## STATUS: READY FOR BOSS REVIEW (Demo) BUT NEEDS PROPER SOLUTION BEFORE PRODUCTION

✅ Show current fix to boss tomorrow (demo-hykudev works perfectly)
⚠️ Clarify it's proof-of-concept pending upstream architecture investigation
🔧 After approval, proceed with design phase (investigate homepage, proper solution, test on production data)
