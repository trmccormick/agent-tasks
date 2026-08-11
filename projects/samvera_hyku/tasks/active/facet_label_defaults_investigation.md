# Investigate Facet Label Auto-generation & Defaults

**Status:** Not Started  
**Priority:** Medium  
**Source:** WVU Knapsack soft launch (identified 2026-08-10)

## Problem
M3 profile flexible schema auto-generates facets (e.g., `people_represented_sim`) without explicit Blacklight facet field labels or i18n keys.

**Symptom:** Facets display untranslated Blacklight keys (e.g., "blacklight.search.fields.show.people_represented_tesim") instead of user-friendly labels.

## Current Workaround in Knapsack
**Approach 1 (i18n):** Created `config/locales/blacklight.en.yml` with explicit translations
**Approach 2 (Decorator):** Created `app/controllers/catalog_controller_decorator.rb` with label configuration

Both approaches required downstream customization—not ideal for multi-tenant platforms.

## Tasks
- [ ] Investigate how Hyku handles flexible schema facet generation
- [ ] Check if facet labels can be auto-derived from M3 profile metadata
- [ ] Review Blacklight's facet label resolution chain
- [ ] Determine if better defaults can be provided upstream
- [ ] Check Hyku's i18n locale files for facet label patterns

## Impact
- Affects any Hyku instance using flexible schemas
- Multi-tenant systems need consistent facet labeling
- Better upstream defaults reduce downstream burden

## Questions to Investigate
1. Where are facets auto-generated from M3 profile?
2. Can facet labels be extracted from profile metadata?
3. Should Hyku provide default i18n keys for common facets?
4. Is this a Hyku or Hyrax concern?

## Notes
- Knapsack solution works but requires configuration in every deployment
- Better to fix at source (Hyku schema generation)
