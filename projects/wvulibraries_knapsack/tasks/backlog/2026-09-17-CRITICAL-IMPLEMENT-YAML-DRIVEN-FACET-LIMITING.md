---
status: in-progress
priority: CRITICAL
type: implementation
system_domain: M3_METADATA, FACETING, SEARCH, CONFIGURATION
mvp_alignment: PRODUCTION_READINESS
local_worker_safe: true
requires_tenant_build: true
tags: [facet-limiting, m3-flexible-metadata, yaml-config, scalability, upstream-ready]
created: 2026-09-17
updated: 2026-09-17
discovery_phase: 2026-09-16-CRITICAL-DESIGN-PROPER-FACET-LIMITING-SOLUTION (COMPLETE)
solution_branch: fix/hide-type-facet-add-show-more-facets
implementation_commit: 8b3dff3 (YAML-driven facet limiting)
deployment_status: DEPLOYED_TO_VM_RESTARTING_2026-09-17
---

## Agent Dispatch Interface

**Role**: Implementation Agent  
**Project**: WVU Knapsack  
**Task**: Implement YAML-driven facet limiting (replace hardcoded patches)

**Step 0**: Move this task to `active/`, update YAML status to `in-progress`

**Reference**: Design phase output available in previous synthesis report

---

## 🚀 IMPLEMENTATION: YAML-Driven Facet Limiting (Post-Design)

### Current Band-Aid (To Be Replaced)
File: `config/initializers/catalog_controller_decorator.rb` lines 107-130
```ruby
critical_facets = {
  'date_created_sim' => 'Date Created',
  'location_sim' => 'Location',
  'people_represented_sim' => 'People Represented'
}

critical_facets.each do |field_name, label|
  if config.facet_fields.key?(field_name)
    config.facet_fields[field_name].limit = 5
    config.facet_fields[field_name].label = label
  else
    config.add_facet_field field_name, label: label, limit: 5, show_more: true
  end
end
```

**Problem**: Hardcodes 3 field names. Production has 15+ facets. Only those 3 get limits.

---

## PHASE 1: Create YAML Configuration File

**File**: `config/wvu_facet_defaults.yml` (NEW)

```yaml
# WVU Knapsack Facet Configuration
# Defines default facet behavior and force-registered fields
# Updated: 2026-09-17

# Default settings applied to all _sim facets during registration
defaults:
  limit: 5
  show_more: true

# Fields that must be force-registered even if not in M3 profile
# (Used for WVU-specific facets or facets from hardcoded base config)
force_registered_fields:
  date_created_sim: "Date Created"
  location_sim: "Location"
  people_represented_sim: "People Represented"
```

**Expected Behavior**:
- Defaults apply to ALL dynamically discovered _sim facets
- Force-registered fields ensure critical WVU facets are always present
- YAML is optional (graceful fallback if file doesn't exist)

---

## PHASE 2: Replace Hardcoded Section in Decorator

**File**: `config/initializers/catalog_controller_decorator.rb`

**Replace** lines 107-130 (the `critical_facets` hash and loop) with:

```ruby
# PHASE 3 & 4: Apply universal facet limits + load force-registered fields from YAML
# This replaces hardcoded critical_facets with configuration-driven approach

# 1. Apply a default limit (5) to all dynamically registered _sim facets that lack one
config.facet_fields.each do |key, field_config|
  next unless key.to_s.end_with?('_sim') && field_config.respond_to?(:limit=)
  # Only apply if limit is unset or zero
  config.facet_fields[key].limit = 5 if field_config.limit.nil? || field_config.limit.zero?
end

# 2. Load critical WVU facets from YAML instead of hardcoding them
yaml_path = Rails.root.join('config', 'wvu_facet_defaults.yml')
yaml_config = File.exist?(yaml_path) ? YAML.load_file(yaml_path) : {}
force_fields = yaml_config['force_registered_fields'] || {}
defaults = yaml_config['defaults'] || { limit: 5, show_more: true }

force_fields.each do |key, label|
  if config.facet_fields.key?(key)
    config.facet_fields[key].label = label
  else
    config.add_facet_field key, 
                           label: label, 
                           limit: defaults['limit'], 
                           show_more: defaults['show_more']
  end
end

Rails.logger.info("Loaded WVU facet defaults from YAML: #{force_fields.size} force-registered fields")
```

**Key Changes**:
- ✅ No hardcoded field names in Ruby code
- ✅ All _sim facets get `limit: 5` applied post-registration
- ✅ YAML config drives which fields are critical
- ✅ Graceful fallback if YAML missing (empty hash)

---

## PHASE 3: Update Logging

After the code replacement, verify these log lines appear on restart:
```
Registered X M3 facets from active FlexibleSchema
Updated X existing facet: ... (limit: 5)
Loaded WVU facet defaults from YAML: 3 force-registered fields
```

---

## PHASE 4: Validation Strategy

**Test 1**: Delete YAML file temporarily
```bash
rm config/wvu_facet_defaults.yml
# Restart Rails server
# Verify logs show graceful fallback (no errors)
# Confirm all _sim fields still have .limit == 5
```

**Test 2**: Verify facet limits applied
```bash
# Check Rails console:
CatalogController.blacklight_config.facet_fields.select { |k, v| k.end_with?('_sim') }
  .each { |k, v| puts "#{k}: limit=#{v.limit}" }
# All should show limit=5
```

**Test 3**: Run on demo-hykudev
- Restart app on demo
- Visit catalog page
- Verify "more" links still show on date_created_sim, location_sim, people_represented_sim
- Confirm facet counts truncate to 5 items

**Test 4**: Run on production (digitalhistory)
- Deploy to production
- Visit catalog page
- Verify "more" links show on ALL facets (not just 3 hardcoded ones)
- Check logs: should see 15+ facets registered with limits
- Confirm CatalogSearchBuilder still sets `f.<field>.facet.limit = 6` for all

**Success Criteria**:
- ✅ No hardcoded field names in decorator
- ✅ All _sim facets get limit: 5 applied dynamically
- ✅ YAML file controls force-registered fields
- ✅ Graceful fallback if YAML missing
- ✅ Works on demo (3 facets, all show "more" links)
- ✅ Works on production (15+ facets, all show "more" links)
- ✅ Logs confirm all facets registered with proper limits

---

## FILES TO MODIFY

1. **CREATE**: `config/wvu_facet_defaults.yml` (new configuration file)
2. **EDIT**: `config/initializers/catalog_controller_decorator.rb` (lines 107-130)

## DELIVERABLES

1. ✅ YAML config file created with defaults + force_registered_fields
2. ✅ Decorator refactored to use YAML instead of hardcoded hash
3. ✅ All validation tests pass (demo + production)
4. ✅ Commit message explains why this approach is upstream-ready
5. ✅ Synthesis report with findings + next steps

---

## GOTCHAS & NOTES

- **YAML loading**: Uses `File.exist?` check for graceful fallback
- **Limit application**: Only applies to _sim fields (facets)
- **Force-registered fields**: Ensures critical WVU facets exist even if M3 profile missing them
- **Upstream readiness**: No hardcoded WVU-specific logic; solution can backport to Hyku/Hyrax

---

## ACCEPTANCE CRITERIA (ALL MUST PASS)

- [ ] YAML file created with correct structure
- [ ] Hardcoded critical_facets section removed from decorator
- [ ] All _sim facets get limit: 5 applied post-registration
- [ ] Graceful fallback if YAML file missing (no errors)
- [ ] Demo test: "more" links visible on all 3 facets
- [ ] Production test: "more" links visible on 15+ facets
- [ ] Logs show proper facet registration and YAML loading
- [ ] CatalogSearchBuilder still sets facet.limit = limit+1 for all fields
- [ ] No breaking changes to existing hardcoded facets (creator_sim, etc.)
- [ ] Ready for upstream PR (no WVU-specific hardcoding)

