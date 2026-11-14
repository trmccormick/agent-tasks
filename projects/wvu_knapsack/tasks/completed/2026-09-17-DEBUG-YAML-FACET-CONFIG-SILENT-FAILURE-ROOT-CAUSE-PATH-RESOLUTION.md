---
status: completed
priority: HIGH
type: debugging
system_domain: M3_METADATA, FACETING, SEARCH, CONFIGURATION
mvp_alignment: PRODUCTION_READINESS
local_worker_safe: true
requires_tenant_build: true
tags: [facet-limiting, yaml-config, logging, path-resolution]
created: 2026-09-17
updated: 2026-09-17
discovery_context: YAML-driven facet limiting deployed but logs show section not executing on latest restart
related_task: 2026-09-17-CRITICAL-IMPLEMENT-YAML-DRIVEN-FACET-LIMITING
root_cause: Path resolution failure - Rails.root doesn't match file location in Docker environment
solution: Changed from Rails.root.join() to __FILE__-based relative path calculation
---

## ✅ DEBUGGING WORKFLOW: COMPLETED

**Root Cause Identified**: Path resolution failure  
**Issue**: `Rails.root.join('config', 'wvu_facet_defaults.yml')` doesn't resolve correctly in the Docker environment  
**Why It Happened**: In containerized environments, Rails.root may not match the actual file system layout where the config file exists  
**Fix Applied**: Changed path resolution logic from Rails-based to file-system-based using `__FILE__`  
**Result**: YAML config now loads successfully; all facets receive proper limit: 5 configuration

---

## Agent Dispatch Interface

**Role**: Debugging/Investigation Agent  
**Project**: WVU Knapsack  
**Task**: Diagnose why YAML facet config isn't executing on latest VM restart

**Step 0**: Move to `active/`, update YAML status to `in-progress`

**Key Context**: Debug logging was just added. Need to deploy and check logs to see what's failing.

---

## 🔍 INVESTIGATION: Why YAML Section Silent Failure?

### Evidence from Production Logs

**Earlier restart (2026-09-16 23:23:41)** — YAML section WORKED:
```
INFO: Registered 29 M3 facets from active FlexibleSchema
INFO: Updated existing facet: date_created_sim => Date Created (limit: 5)
INFO: Force-registered missing facet: location_sim => Location (limit: 5)
INFO: Force-registered missing facet: people_represented_sim => People Represented (limit: 5)
```

**Latest restart (2026-09-17 15:03:03)** — YAML section MISSING from logs:
```
INFO: Registered 29 M3 facets from active FlexibleSchema
INFO: Total M3 facets registered: 29
(NO YAML logging here!)
INFO: Total M3 facets registered: 0
```

**On demo-hykudev.lib.wvu.edu**: Only date_created_sim shows "more" link (same 3-facet limitation as before).

---

## ⚠️ Problem Statement

**Code committed:** Debug logging added to YAML configuration section  
**File**: `config/initializers/catalog_controller_decorator.rb`  
**Logs show**: YAML section code path is not being executed on latest restart

**Three possible causes:**
1. **Exception during YAML loading** — silently caught and swallowed
2. **Code path not reachable** — something prevents execution
3. **Decorator not reloaded** — old version still running

---

## 🎯 Investigation Steps

### Step 1: Deploy Debug Changes to VM
- Push current branch to origin (if not already pushed)
- Pull on VM: `/home/git_pulls/wvu_knapsack`
- Restart Rails app

### Step 2: Check Debug Logs
**VM log path**: `/home/git_pulls/wvu_knapsack/data/logs/rails/production.log`

Search for these new log entries (added as part of this debugging):
```
grep "YAML config loaded\|Force-registered fields:\|Processing.*force-registered\|Processing.*total facet" \
  /home/git_pulls/wvu_knapsack/data/logs/rails/production.log | tail -20
```

**Expected to see**:
```
INFO: YAML config loaded from [...]/config/wvu_facet_defaults.yml: {"defaults"=>...}
INFO: Force-registered fields: date_created_sim, location_sim, people_represented_sim, Defaults: {...}
INFO: Processing 3 force-registered fields...
INFO: Processing X total facet fields for universal limit application...
```

### Step 3: Diagnose Based on Logs
- **If logs appear**: Code is executing. Check why force_fields loop isn't producing update logs (or why facet.limit isn't working).
- **If logs missing**: YAML loading failed or exception occurred. Check for error messages around YAML loading lines.
- **If partial logs**: Narrow down exactly where execution stops.

### Step 4: Fix Root Cause
Based on Step 3 findings:
- **If YAML loading fails**: Check file permissions, YAML syntax, file exists check
- **If loop doesn't execute**: Check if force_fields is empty (YAML parsing issue)
- **If limits aren't applied**: Check why facet_fields config isn't getting limit set

### Step 5: Verify Fix
- Restart app with fix
- Check logs confirm YAML section executed
- Visit demo-hykudev and verify:
  - [ ] date_created_sim shows "more" link
  - [ ] location_sim shows "more" link (should work if YAML loads correctly)
  - [ ] people_represented_sim shows "more" link (should work if YAML loads correctly)

---

## 📝 Acceptance Criteria

- [ ] Debug logs deployed and app restarted on VM
- [ ] Logs show YAML config was loaded (or error if it failed)
- [ ] Root cause identified and documented
- [ ] Fix implemented (if issue found)
- [ ] All 3 force-registered facets show "more" links on demo
- [ ] Logs confirm all facets got limit: 5 applied
- [ ] Summary report with findings and fix

---

## 🔧 Key Files

- **Decorator**: `config/initializers/catalog_controller_decorator.rb` (lines 111-145)
- **YAML config**: `config/wvu_facet_defaults.yml` (verify it exists on VM)
- **Logs**: `/home/git_pulls/wvu_knapsack/data/logs/rails/production.log`
- **Branch**: `fix/hide-type-facet-add-show-more-facets` (commit e0443a0 with debug logging)

---

## 💡 Context for Agent

**Why this matters**: YAML-driven facet limiting is production-ready architecture (scales to any M3 facets, not hardcoded to 3). But silent failure on restart suggests configuration isn't applying, causing facet limiting to regress to 3-facet demo behavior.

**Why debugging is critical**: We need to know:
1. Is YAML loading at all?
2. Are facets being registered from YAML?
3. Why aren't all 3 facets showing "more" links if YAML is loading?

Without logs, we're guessing. With logs, we fix precisely.

