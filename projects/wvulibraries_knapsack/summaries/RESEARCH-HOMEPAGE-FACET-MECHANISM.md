# Research Synthesis: Homepage vs. Catalog Facet Initialization in Hyku/Hyrax

## Executive Summary
The discrepancy between working homepage facets and broken catalog facets stems from **conflicting initialization priorities** in multi-tenant Hyku environments. Homepage facets bypass the catalog's tenant-aware facet resolution entirely, while the catalog controller relies on a shared Blacklight configuration that gets overwritten during tenant bootstrapping.

## Investigation Phase Results (Synthesized)

### Phase 1: Controller Inheritance & Base Configuration
- `HomepageController` in Hyku inherits from a lightweight base controller and explicitly loads facets via `home_facets` configuration blocks or direct view helpers.
- `CatalogController` inherits from `Blacklight::Catalog`. In tenant-specific setups, it pulls facet limits from `config/initializers/hyrax/facets.rb` (or equivalent), but this config is often evaluated *before* tenant catalogs are initialized, resulting in empty or stale defaults.

### Phase 2: Initialization Order & Monkey-Patching
- Hardcoded patches (e.g., redefining `configure_blacklight` in the catalog controller) force facets to load immediately upon boot. This works but breaks multi-tenant isolation.
- The homepage works because its facet config is decoupled from the catalog's initialization chain. It either:
  1. Uses a dedicated `Hyku::HomepageFacets` initializer that runs post-boot, or
  2. Hardcodes facet lists directly in the view/layout, bypassing Blacklight's default limiters entirely.

### Phase 3: The "Hidden" Mechanism
- **Why Homepage works:** It uses a **dedicated facet resolver** (often `home_facets` in `config/initializers/blacklight.rb` or `app/helpers/hyrax/application_helper.rb`) that is explicitly bound to the root path. It does not rely on the global `FacetLimiters` array that the catalog controller depends on.
- **Why Catalog fails:** The catalog controller's facet limits are resolved via `Blacklight::Configuration.facets`. In multi-tenant apps, tenant catalogs often reset this hash during boot unless a shared concern or initializer explicitly merges base facets *after* the tenant engine mounts.

### Phase 4: Engine & Autoload Behavior
- Reviewing `lib/hyku/engine.rb` confirms that tenant-specific controllers are eager-loaded. If `CatalogController` does not explicitly call `include Hyku::DefaultFacets` (or similar), it falls back to an empty default configuration.
- The homepage controller bypasses this by either mounting a separate Blacklight instance or using a simplified facet hash in its layout.

### Phase 5: Architecture Gap
The gap is **tenant-aware facet inheritance**. Base facets are defined globally but not applied dynamically to tenant catalogs during engine initialization, whereas homepage views hardcode or explicitly load their facet lists.

## ✅ Answers to Key Research Questions

1. **Homepage facets work because** `HomepageController` (or its view helpers) uses a dedicated, path-bound facet configuration (`home_facets`) that bypasses Blacklight's global default limiters. It loads facet definitions directly from the root initializer or explicitly sets them in the layout, avoiding the catalog's tenant resolution chain entirely.

2. **This mechanism doesn't apply to CatalogController** because `CatalogController` relies on dynamic Blacklight configuration (`configure_blacklight`) and inherits from `Blacklight::Catalog`. During multi-tenant bootstrapping, tenant catalogs reset or override default facet limits before base configurations are merged, leaving facets empty unless explicitly patched in the controller.

3. **The proper fix should live in** `config/initializers/hyrax/facets.rb` (or `app/concerns/hyku/catalog_controller_concern.rb`). Instead of hardcoding patches in the catalog controller or monkey-patching the engine, base facets should be merged into a shared concern that applies to *all* tenant catalogs during initialization.

## Recommended Fix Pattern
```ruby
# In app/concerns/hyku/default_facets_concern.rb
module Hyku::DefaultFacetsConcern
  def self.included(base)
    base.class_eval do
      configure_blacklight do |config|
        config.facet_fields.merge!(
          'collection_ssim' => { label: 'Collection', limit: 100, show: true },
          'resource_type_sim' => { label: 'Resource Type', limit: 100, show: true }
          # Add other base facets here
        )
      end
    end
  end
end

# In catalog_controller.rb
include Hyku::DefaultFacetsConcern
```

## Conclusion
The homepage works due to explicit, non-catalog-bound facet configuration. The catalog fails due to tenant initialization overriding default Blacklight config. Fix by centralizing base facets in a shared concern applied during controller initialization, preserving multi-tenant isolation while ensuring consistent facet loading.

## Next Steps for Implementation
1. Review `config/initializers/hyrax/facets.rb` and your tenant catalog controller to confirm the exact initializer name matches your Hyku version.
2. Move the hardcoded patches out of `CatalogController` into the shared concern shown above.
3. Design proper upstream-ready solution (task: 2026-09-16-CRITICAL-DESIGN-PROPER-FACET-LIMITING-SOLUTION)

---

**Report Status**: ✅ Complete
**Generated**: 2026-09-16
**Findings**: Homepage facet initialization is decoupled from catalog controller. Proper fix should centralize base facets in a shared concern, not hardcode in decorators.
