# Handoff: Upstream Contribution Investigation — 2026-08-11

**From:** Tracy (WVU Knapsack soft launch completion)  
**To:** Qwen Planning Agent  
**Date:** 2026-08-11  
**Focus:** Hyku main branch investigation for upstream PR candidates

---

## Context

WVU Knapsack soft launch is complete with all critical issues fixed and tested on production. During development, we identified 3 issues that could be contributed back to Hyku/Hyrax core. This work investigates whether fixes already exist upstream and prepares PR candidates if not.

**Knapsack Repo:** `/Users/tam0013/Documents/git/wvu_knapsack`  
**Branch:** `fix/facet-links-and-hide-type-facet` (8 commits, tested on hykudev production)

---

## Investigation Tasks (In Priority Order)

### Task 1: Wings::ModelRegistry NameError Fix Investigation
**File:** `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/active/investigate_wings_modelregistry_fix.md`

**The Issue:**
- Hyrax::Goddess::Query#model_class_for throws `NameError: uninitialized constant Wings::ModelRegistry`
- Occurs during work show page rendering when Wings::ModelRegistry is not defined
- Affects initialization order on some deployments

**Knapsack Workaround:**
Located in: `wvu_knapsack/config/initializers/goddess_query_fix.rb`
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

**Your Investigation Steps:**
1. Check Hyku main branch for existing fix (search PRs, recent commits)
2. Check Hyrax gem for the same issue
3. If not fixed: Determine proper ownership (Hyku, Hyrax, or Wings)
4. Document findings and prepare PR candidate

**Impact:** Small defensive patch, backwards compatible, suitable for patch releases

---

### Task 2: Flexible Schema Facet Label Auto-generation
**File:** `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/active/facet_label_defaults_investigation.md`

**The Issue:**
- M3 profile flexible schema auto-generates facets (e.g., `people_represented_sim`)
- No explicit Blacklight facet field labels or i18n keys provided
- Results in untranslated keys or ugly auto-formatted names (e.g., "Creator Sim" instead of "Creator")

**Knapsack Workarounds:**
1. i18n approach: `wvu_knapsack/config/locales/blacklight.en.yml`
2. Config decorator: `wvu_knapsack/app/controllers/catalog_controller_decorator.rb` with explicit labels

**Your Investigation Steps:**
1. Locate Hyku's flexible schema facet generation code
2. Check how M3 profile metadata could provide facet labels
3. Review Blacklight's facet label resolution chain
4. Investigate if Hyku provides default i18n keys for common facets
5. Assess feasibility of better upstream defaults

**Questions to Answer:**
- Where/how are facets auto-generated from M3 profile?
- Can facet labels be extracted from profile metadata?
- Should Hyku provide default i18n keys for common facets?
- Is this a Hyku or Hyrax concern?

**Impact:** Medium priority, affects any Hyku instance using flexible schemas, reduces downstream burden

---

### Task 3: Facet Label Resolution Documentation
**File:** `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyrax/tasks/investigate_facet_label_documentation.md`

**The Issue:**
- Blacklight's facet label resolution chain not well documented
- Downstream projects struggle with facet naming and labeling
- Resolution chain is: config label → i18n key → auto-formatted field name

**Knapsack Solution:**
Updated decorator with explicit labels (see Task 2 workaround)

**Your Investigation Steps:**
1. Review Hyrax documentation on facet customization
2. Check if facet label resolution chain is documented
3. Prepare documentation PR for Hyrax with:
   - Facet label resolution chain explanation
   - Best practices for configuring facet labels
   - Examples (i18n vs. config approaches)
   - Multi-tenant deployment guidance
   - Common field naming conventions and labels
4. Include sample `blacklight.en.yml` locale file

**Impact:** Low priority, documentation-only, saves time for downstream developers

---

## Technical Context

**Knapsack Stack:**
- Rails 7.2.3, Ruby 3.3.10
- Hyrax 5.2.0 (gem dependency)
- Hyku 7.1.0 (via hyrax-webapp submodule)
- Blacklight for catalog interface
- Multi-tenant via Hyku

**Current Branch Status:**
- All fixes committed, tested, pushed to production (hykudev)
- hyrax-webapp submodule kept clean (no direct modifications)
- Using overrides, decorators, initializers for all customizations

**Architectural Principle:**
Knapsack never modifies the hyrax-webapp submodule. All fixes use:
- View overrides (`app/views/`)
- Decorators (`app/controllers/*_decorator.rb`)
- Initializers (`config/initializers/`)
- Config changes (`config/`)

---

## Resources

**Related Files in Knapsack:**
- Workaround code: `wvu_knapsack/config/initializers/goddess_query_fix.rb`
- Facet labels: `wvu_knapsack/app/controllers/catalog_controller_decorator.rb`
- i18n: `wvu_knapsack/config/locales/blacklight.en.yml`
- Homepage facet rendering: `wvu_knapsack/app/views/hyrax/homepage/_facet_limit.html.erb`

**Hyku/Hyrax Repos:**
- Hyku: https://github.com/samvera/hyku
- Hyrax: https://github.com/samvera/hyrax

---

## Success Criteria

✅ Complete when:
1. Task 1: Determine if Wings::ModelRegistry fix exists upstream, prepare PR candidate if not
2. Task 2: Document flexible schema facet generation approach, identify where improvements could be made
3. Task 3: Document findings for Hyrax documentation, prepare PR if applicable

🎯 Ideal Outcome: 1-3 PRs submitted to Hyku/Hyrax with valuable upstream contributions

---

## Next Steps After Investigation

1. If fixes don't exist upstream → Prepare focused PRs for each
2. If fixes exist → Document findings and update Knapsack notes
3. Share findings with Knapsack team for architectural learnings
4. Consider each fix for inclusion in next Hyku/Hyrax releases
