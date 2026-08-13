# Investigate Facet Label Auto-generation & Defaults

**Status:** Investigation Complete — Enhancement Needed  
**Priority:** Medium  
**Source:** WVU Knapsack soft launch (identified 2026-08-10)  
**Investigation Date:** 2026-08-11  

## Problem
M3 profile flexible schema auto-generates facets (e.g., `people_represented_sim`) without explicit Blacklight facet field labels or i18n keys.

**Symptom:** Facets display untranslated Blacklight keys (e.g., "blacklight.search.fields.show.people_represented_tesim") instead of user-friendly labels.

## Current Workaround in Knapsack
**Approach 1 (i18n):** Created `config/locales/blacklight.en.yml` with explicit translations
**Approach 2 (Decorator):** Created `app/controllers/catalog_controller_decorator.rb` with label configuration

Both approaches required downstream customization—not ideal for multi-tenant platforms.

## Investigation Findings (2026-08-11)

### How Hyku Handles Facets Today
- `catalog_controller.rb` has **hardcoded facet fields** with explicit labels for the standard set (lines 126-143)
- Standard facets (`resource_type_sim`, `contributor_sim`, etc.) have explicit `label:` values
- Some use `helper_method:` (e.g., `generic_type_facet_label`, `work_type_facet_label`) for dynamic label resolution
- **No mechanism exists** to auto-generate facet labels from M3 profile metadata

### Blacklight Facet Label Resolution Chain
1. `label:` option on `add_facet_field`
2. i18n key (`blacklight.search.facets.{field_name}`)
3. Helper method (if `helper_method:` specified)
4. Auto-formatted field name (e.g., `"People Represented Sim"`)

### Root Cause
When flexible schemas add new fields (e.g., `people_represented_sim`), Blacklight falls through the chain with no explicit label set, resulting in ugly auto-formatted names or untranslated i18n keys.

### Ownership Assessment
**Hyku concern** — the flexible schema facet generation happens in Hyku's M3 profile handling, not in core Hyrax.

## Tasks
- [x] Investigate how Hyku handles flexible schema facet generation
- [x] Check if facet labels can be auto-derived from M3 profile metadata
- [x] Review Blacklight's facet label resolution chain
- [ ] Determine if better defaults can be provided upstream (enhancement, ~2-4 hours)
- [x] Check Hyku's i18n locale files for facet label patterns

## Impact
- Affects any Hyku instance using flexible schemas
- Multi-tenant systems need consistent facet labeling
- Better upstream defaults reduce downstream burden

## Questions to Investigate
1. Where are facets auto-generated from M3 profile? → Hyku's M3 profile handling (not in core Hyrax)
2. Can facet labels be extracted from profile metadata? → Yes, by stripping `_sim`/`_tesim` suffixes and camel-casing
3. Should Hyku provide default i18n keys for common facets? → Yes, medium-priority enhancement
4. Is this a Hyku or Hyrax concern? → **Hyku** — flexible schema facet generation is Hyku-specific
