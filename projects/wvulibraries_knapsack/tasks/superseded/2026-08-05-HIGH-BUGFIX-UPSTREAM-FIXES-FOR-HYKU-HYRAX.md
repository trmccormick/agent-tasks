---
status: backlog
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/backlog/2026-08-05-HIGH-BUGFIX-UPSTREAM-FIXES-FOR-HYKU-HYRAX.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvulibraries_knapsack/tasks/backlog/2026-08-05-HIGH-BUGFIX-UPSTREAM-FIXES-FOR-HYKU-HYRAX.md \
         projects/wvulibraries_knapsack/tasks/active/2026-08-05-HIGH-BUGFIX-UPSTREAM-FIXES-FOR-HYKU-HYRAX.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/wvulibraries_knapsack/tasks -name "2026-08-05-HIGH-BUGFIX-UPSTREAM-FIXES-FOR-HYKU-HYRAX.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.
```

---

## 🎯 Objective

Create consolidated upstream patches for Hyku/Hyrax that fix 5 bugs currently patched in Knapsack's `fix/facet-links-and-hide-type-facet` branch. Each patch should be a standalone initializer/decorator that can be submitted as a PR to the appropriate upstream repo (Hyku or Hyrax).

**Source**: Audit documented at `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/UPSTREAM_CONTRIBUTION_AUDIT.md`
**Current Branch**: `fix/facet-links-and-hide-type-facet` in `/Users/tam0013/Documents/git/wvu_knapsack`

---

## 📋 Task Breakdown — 5 Upstream Fixes

### Fix #1: Sprockets Asset Pipeline — Missing blacklight_advanced_search Requires

**Upstream Target**: Hyku (`hyku-core`)
**Current Knapsack File**: `config/initializers/sprockets_directive_patch.rb`
**Branch Path**: `/Users/tam0013/Documents/git/wvu_knapsack/config/initializers/sprockets_directive_patch.rb`

**Bug**: hyrax-webapp's `application.js` and `application.css` contain `*= require blacklight_advanced_search` directives that reference a gem whose assets are not installed. This causes 500 errors on every page load during asset compilation.

**Current Fix**: Registers Sprockets preprocessors to strip missing requires before compilation.

```ruby
# Current implementation (from Knapsack):
if defined?(Sprockets)
  Sprockets.register_preprocessor('text/css', -> (input) {
    data = input[:data]
    data.gsub(%r{^\s*\*=\s*require\s+blacklight_advanced_search\s*$}, '')
  })
  Sprockets.register_preprocessor('application/javascript', -> (input) {
    data = input[:data]
    data.gsub(%r{^//=\s*require\s+blacklight_advanced_search\s*$}, '')
  })
end
```

**What to Do**:
1. Read the current Knapsack implementation at the branch path above
2. Create a PR-ready patch for Hyku that either:
   - Removes the broken requires from hyrax-webapp's asset files directly, OR
   - Registers Sprockets preprocessors in a Hyku initializer (preferred — less invasive)
3. Document why this fix is needed and what error it prevents

**Acceptance Criteria**:
- [ ] Patch can be applied to a fresh Hyku install without errors
- [ ] Asset compilation succeeds even when blacklight_advanced_search assets are missing
- [ ] No side effects on other asset pipelines

---

### Fix #2: Valkyrie Resource Resolver — Missing Wings::ModelRegistry Guard

**Upstream Target**: Hyku (`hyku-core`) or Hyrax (wings gem)
**Current Knapsack Files**: 
- `config/initializers/valkyrie_resource_resolver_override.rb`
- `config/initializers/wings_fix.rb` (duplicate — consolidate into one)
**Branch Paths**:
- `/Users/tam0013/Documents/git/wvu_knapsack/config/initializers/valkyrie_resource_resolver_override.rb`
- `/Users/tam0013/Documents/git/wvu_knapsack/config/initializers/wings_fix.rb`

**Bug**: hyrax-webapp's `config/initializers/wings.rb` sets `Valkyrie.config.resource_class_resolver` to a lambda that calls `Wings::ModelRegistry.reverse_lookup(klass)` without checking if the constant is defined. When Wings isn't loaded at initialization time, this crashes with `NameError (uninitialized constant Wings::ModelRegistry)`.

**Current Fix**: Overrides the resolver with proper guards (`defined?`, `respond_to?`, rescue).

```ruby
# Current implementation (consolidated from both files):
Valkyrie.config.resource_class_resolver = lambda do |resource_klass_name|
  klass = resource_klass_name.gsub(/Resource$/, '').constantize
  
  if defined?(Wings::ModelRegistry) && Wings::ModelRegistry.respond_to?(:reverse_lookup)
    begin
      Wings::ModelRegistry.reverse_lookup(klass) || klass
    rescue NameError, NoMethodError
      klass
    end
  else
    klass
  end
end
```

**What to Do**:
1. Read both current Knapsack implementations
2. Create a single consolidated patch for Hyku that:
   - Patches `wings.rb` initializer to guard against missing constants
   - OR overrides the resolver in a Hyku initializer (preferred — less invasive)
3. Document the race condition/timing issue that causes this bug

**Acceptance Criteria**:
- [ ] Patch handles case where Wings::ModelRegistry is not defined
- [ ] Patch handles case where reverse_lookup method doesn't exist
- [ ] Graceful fallback to klass when lookup fails
- [ ] No errors during Rails initialization or code reload

---

### Fix #3: Render Constraints Infinite Recursion in Module.prepend

**Upstream Target**: Hyku (`hyku-core`) or blacklight_advanced_search gem
**Current Knapsack File**: `lib/blacklight_advanced_search/render_constraints_override_decorator.rb`
**Branch Path**: `/Users/tam0013/Documents/git/wvu_knapsack/lib/blacklight_advanced_search/render_constraints_override_decorator.rb`

**Bug**: The decorator used `original_blacklight_method = Blacklight::RenderConstraintsHelperBehavior.instance_method(__method__)` with `Module.prepend`, which creates infinite recursion because `__method__` resolves to the decorated method name.

**Current Fix**: Changed to use `super(params_or_search_state)` pattern (the standard Ruby way to call the prepended method).

```ruby
# Current implementation (fixed):
module BlacklightAdvancedSearch
  module RenderConstraintsOverrideDecorator
    def render_constraints_filters(params_or_search_state = search_state)
      super(params_or_search_state)  # Changed from original_blacklight_method.bind(self).call()
    end
  end
end
```

**What to Do**:
1. Read the current Knapsack implementation
2. Create a PR-ready patch that:
   - Uses `super()` instead of `instance_method(__method__).bind(self).call()`
   - Documents why the original pattern causes infinite recursion
3. Consider if this should go in Hyku or upstream to blacklight_advanced_search gem

**Acceptance Criteria**:
- [ ] No infinite recursion when render_constraints_filters is called
- [ ] Original Blacklight behavior is preserved via super()
- [ ] Works with both prepended and non-prepended contexts

---

### Fix #4: Index Field Link — Bare M3 Property Names Break Facet Drill-Down

**Upstream Target**: Hyku (`hyku-core`) or Hyrax
**Current Knapsack File**: `app/helpers/hyrax/override_helper_behavior.rb` (method: `index_field_link`)
**Branch Path**: `/Users/tam0013/Documents/git/wvu_knapsack/app/helpers/hyrax/override_helper_behavior.rb`

**Bug**: Hyrax v5.2.0 writes bare M3 property names (e.g., "contributor") into catalog URLs as `search_field=contributor`, which targets no real Solr field. Users clicking metadata values on search result rows get broken drill-down links.

**Current Fix**: Prefers `link_to_facet` when configured; otherwise suffixes `_sim` onto the bare name so the URL routes to the indexed field.

```ruby
# Current implementation:
def index_field_link(options)
  raise ArgumentError unless options[:config] && options[:config][:field_name]
  
  facet_field = options[:config].try(:link_to_facet) || options[:config][:link_to_facet]
  
  if facet_field.present?
    safe_join(options[:value].map { |item| link_to_facet(item, facet_field) }, ", ")
  else
    name = options[:config][:field_name].to_s
    name = "#{name}_sim" unless name.match?(/_(sim|ssim|tesim|tsim|ssi|tsi|dtsi)\z/)
    safe_join(options[:value].map { |item| link_to_field(name, item, item) }, ", ")
  end
end
```

**What to Do**:
1. Read the current Knapsack implementation
2. Create a PR-ready patch for Hyrax that:
   - Fixes `HyraxHelperBehavior#index_field_link` to handle bare M3 property names
   - Adds `_sim` suffix when link_to_facet is not configured
   - Preserves existing behavior when link_to_facet IS configured
3. Document the bug with example URLs showing broken vs fixed behavior

**Acceptance Criteria**:
- [ ] Metadata values on search results drill down correctly
- [ ] Works for all indexed field types (_sim, _ssim, _tesim, etc.)
- [ ] link_to_facet configuration is respected when present
- [ ] No regression for non-M3 workflows

---

### Fix #5: Notifications Component — Renders Even When Disabled + WebSocket Origin Validation

**Upstream Target**: Hyku (`hyku-core`) or Hyrax
**Current Knapsack Files**: 
- `app/helpers/hyrax/override_helper_behavior.rb` (method: `render_notifications`)
- `config/initializers/hyrax.rb` (added `config.realtime_notifications = false`)
**Branch Paths**:
- `/Users/tam0013/Documents/git/wvu_knapsack/app/helpers/hyrax/override_helper_behavior.rb`
- `/Users/tam0013/Documents/git/wvu_knapsack/config/initializers/hyrax.rb`

**Bug**: The notifications component renders and attempts WebSocket connections even when `realtime_notifications` is disabled, causing console spam with "Request origin not allowed" errors.

**Current Fix**: 
1. Returns early from `render_notifications` when realtime_notifications is not enabled
2. Explicitly sets `config.realtime_notifications = false` as workaround

```ruby
# Current implementation (render_notifications):
def render_notifications(options = {})
  return ''.html_safe unless Hyrax.config.realtime_notifications?
  super(options)
end

# Current workaround (hyrax.rb):
config.realtime_notifications = false
```

**What to Do**:
1. Read both current Knapsack implementations
2. Create a PR-ready patch for Hyku that:
   - Fixes `render_notifications` to respect the config at render time
   - OR fixes WebSocket origin validation so tenants can opt-in if desired
3. Document why the workaround was needed and what the proper fix enables

**Acceptance Criteria**:
- [ ] Notifications component doesn't render when disabled
- [ ] No WebSocket connection errors in browser console
- [ ] Tenants can still enable realtime_notifications if they want the feature
- [ ] Origin validation works for all tenant domains

---

## 🔧 Prerequisites

1. **Read the audit document first**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/UPSTREAM_CONTRIBUTION_AUDIT.md`
2. **Clone/fetch wvu_knapsack repo**: `/Users/tam0013/Documents/git/wvu_knapsack`
3. **Checkout the branch**: `git checkout fix/facet-links-and-hide-type-facet`
4. **Understand dependency chain**: Hyrax (gem) → Hyku (submodule in hyrax-webapp) → Knapsack (this repo)

---

## 📝 Output Requirements

For each of the 5 fixes, produce:

1. **PR-ready patch file** — A single initializer/decorator that can be dropped into a Hyku/Hyrax install
2. **README section** — Explains the bug, the fix, and why it's needed (for upstream PR description)
3. **Test instructions** — How to verify the fix works in a clean Hyku/Hyrax install

**File naming convention**: `upstream_fix_#N-description.rb` (e.g., `upstream_fix_1-sprockets-asset-pipeline.rb`)

---

## ⚠️ Gotchas

1. **Don't modify hyrax-webapp submodule** — Knapsack never modifies upstream code directly. Patches must be initializers/decorators that work without touching submodules.
2. **Consolidate Fix #2** — Two Knapsack files (`valkyrie_resource_resolver_override.rb` and `wings_fix.rb`) patch the same issue. The upstream patch should be a single file.
3. **Fix #5 is a workaround** — Setting `realtime_notifications = false` prevents the feature entirely. The proper fix would be to fix WebSocket origin validation so tenants can opt-in. Document both approaches.
4. **Test in clean environment** — These patches must work on a fresh Hyku/Hyrax install, not just Knapsack. Verify no Knapsack-specific dependencies exist.
5. **Preserve existing behavior** — Each patch should be backward compatible. Don't break existing tenants who may rely on current (buggy) behavior.

---

## ✅ Verification Steps

After creating all 5 patches:

1. **Create a test branch in wvu_knapsack**: `git checkout -b upstream-patches-test`
2. **Copy patches to Knapsack's initializers** (temporary test location)
3. **Run clean rebuild**: `sh down.sc.local.sh && sh up.sc.local.sh`
4. **Verify no errors in logs**: `sc logs web -f --tail=100`
5. **Test all 3 GitHub issues still work**:
   - Issue #11: Homepage facet modals open correctly
   - Issue #13: Help link removed from navigation
   - Issue #14: Contact link points to LibAnswers
6. **Verify facet labels display correctly**: "People Represented" instead of i18n key
7. **Commit patches to test branch** (ready for upstream PR submission)

---

## 📊 Scope Summary

| Fix | Upstream Target | Knapsack Files | Complexity |
|-----|----------------|----------------|------------|
| #1 Sprockets asset pipeline | Hyku | `config/initializers/sprockets_directive_patch.rb` | Low |
| #2 Valkyrie resource resolver | Hyku/Hyrax | 2 files (consolidate to 1) | Medium |
| #3 Render constraints recursion | Hyku/blacklight_advanced_search | `lib/.../render_constraints_override_decorator.rb` | Low |
| #4 Index field link M3 names | Hyrax | `app/helpers/hyrax/override_helper_behavior.rb` (method: index_field_link) | Medium |
| #5 Notifications/WebSocket | Hyku/Hyrax | 2 files (consolidate to 1) | Medium |

**Total**: 5 patches, ~200 lines of code across all fixes
