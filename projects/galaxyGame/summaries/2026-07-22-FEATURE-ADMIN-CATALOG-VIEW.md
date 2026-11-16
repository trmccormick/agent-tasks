# Synthesis: Admin Catalog View

**Date**: 2026-07-22
**Task**: 2026-07-12-HIGH-FEATURE-ADMIN-CATALOG-VIEW.md

## Summary
Build a unified admin catalog view for all blueprints + operational data entries with thumbnails, filters, search, and detail views.

## Architecture Decisions

### Data Access Pattern
- Use `Rails.root.join('..', '..', 'data', 'json-data')` to access gitignored data directory
- Load files lazily — don't read all 447 JSON files on every request
- Cache the catalog index in a service class with stale-while-revalidate pattern
- Use `Dir.glob` for efficient file discovery

### Image Handling
- images/ directory is empty — use Font Awesome placeholder icons
- Placeholder icon type based on category (rocket for crafts, building for structures, etc.)
- When images are generated later, swap to actual image tags automatically

### Performance Strategy
- 447 total entries — pagination with 24 per page
- Lazy-load thumbnails via AJAX (only load JSON data when viewing detail)
- Debounce search input at 300ms
- Category/subcategory filters use URL params (no JS reload)

### Routing
- Add to existing `namespace :admin` routes
- `/admin/catalog` — index
- `/admin/catalog/:id` — show (detail view)
- ID format: `category/filename_without_ext` (e.g., `crafts/ground/harvesting_rover`)

## Files to Create
1. `app/controllers/admin/catalog_controller.rb`
2. `app/views/admin/catalog/index.html.erb`
3. `app/views/admin/catalog/show.html.erb`
4. `app/assets/stylesheets/admin/catalog.scss`
5. `app/javascript/admin/catalog.js`
6. Route additions in `config/routes.rb`

## Risks & Mitigations
- **Data path accessibility in Docker**: Use env var fallback `CATALOG_DATA_PATH` with default to parent of Rails.root
- **JSON parsing errors**: Wrap each file read in rescue, skip invalid JSON gracefully
- **Memory for 447 entries**: Only load metadata (name, type, category) not full JSON on index page
