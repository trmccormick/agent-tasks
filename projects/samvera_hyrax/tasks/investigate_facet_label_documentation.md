# Document Facet Label Resolution & Best Practices

**Status:** Not Started  
**Priority:** Low  
**Source:** WVU Knapsack soft launch (identified 2026-08-10)

## Problem
Blacklight's facet label resolution chain is not well documented or intuitive for downstream projects.

**Resolution Chain (Current Behavior):**
1. Explicit `label:` in config (e.g., `config.add_facet_field 'creator_sim', label: "Creator"`)
2. i18n key translation (e.g., `blacklight.search.facets.creator_sim`)
3. Fallback to field name with auto-formatting (e.g., "creator_sim" → "Creator Sim")

## Issue
Without explicit labels, facets display auto-formatted field names which are often non-intuitive or ugly (e.g., "Creator Sim" instead of "Creator").

## Current Solution in Knapsack
Updated [app/controllers/catalog_controller_decorator.rb](../../../../../wvu_knapsack/app/controllers/catalog_controller_decorator.rb) with explicit labels:
```ruby
config.facet_fields['creator_sim']&.label = "Creator"
config.facet_fields['keyword_sim']&.label = "Keyword"
# etc.
```

## Contribution Opportunity
Create Hyrax documentation for:
- [ ] Blacklight facet label resolution chain explanation
- [ ] Best practices for configuring facet labels
- [ ] Examples showing i18n vs. config approaches
- [ ] Guidance for multi-tenant deployments
- [ ] Common field naming conventions and their labels

## Impact
- Saves time for downstream developers
- Prevents ugly facet labels in new installations
- Establishes clear patterns for facet customization

## Suggested Documentation Location
- Hyrax Customization Guide → Search/Catalog Customization → Facet Configuration

## Notes
- Documentation-only contribution
- No code changes required
- Could also include sample `blacklight.en.yml` locale file
