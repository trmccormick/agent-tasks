---
status: active
priority: LOW
type: verification
system_domain: QA
mvp_alignment: OTHER
local_worker_safe: false
date_created: 2026-08-07
date_assigned: 2026-08-07
---

# ✅ Verify Wings::ModelRegistry Decorator Fix — Bulkrax Importer

**What Was Fixed**: Hyku decorator that guards `Wings::ModelRegistry` lookup when Wings is disabled.

**File Added**: `lib/hyrax/goddess/query_method_missing_machinations_decorator.rb`

**Git Commit**: `e20f31e9` on branch `fix/ga-tenant-property-scoping`

---

## Quick Verification Steps

### Step 1: Start Stack
```bash
cd /Users/tam0013/Documents/git/hyku
sh up.sc.local.sh
# or: sc up -d
```

Wait for web service to boot:
```bash
sc logs web -f
# Watch for: "Listening on http://0.0.0.0:3000"
```

### Step 2: Load Bulkrax Importer Form
Navigate to: `http://localhost:3000/importers/new` (or your local Hyku URL + `/importers/new`)

### Step 3: Verify No NameError
**Expected**: Form loads successfully with no error

**Old Error** (should NOT see):
```
NameError
uninitialized constant Wings::ModelRegistry
```

**If successful**: Form shows "New Importer" with Admin Sets dropdown populated

### Step 4: Verify Decorator Loaded (Optional)
In Rails console (if needed):
```bash
sc exec web bundle exec rails c
> Goddess::Query::MethodMissingMachinations.ancestors.map(&:to_s).grep(/Hyku::GoddessQuery/).first
# Should show: "Hyku::GoddessQueryMethodMissingMachinationsDecorator"
```

---

## What The Fix Does

**Problem**: Hyrax's Goddess::Query#model_class_for unconditionally called `Wings::ModelRegistry.lookup(model)` even when Wings is disabled, causing NameError.

**Solution**: Hyku decorator prepends to override that method:
1. Try to resolve via `internal_resource` if present
2. Only call `Wings::ModelRegistry.lookup` if `defined?(Wings::ModelRegistry)`
3. Fall back to safe constantize if Wings not available
4. Return model itself as final fallback

**Result**: Query setup works for Bulkrax importer UI even in no-Wings mode

---

## Acceptance Criteria

- ✅ Page `/importers/new` loads without NameError
- ✅ Admin Sets dropdown is populated (not empty)
- ✅ Form is ready for import (can proceed to next step)
- ✅ No errors in Rails logs during page load

---

## If Verification Passes

1. Post in chat: "✅ Bulkrax importer form loads successfully. Wings decorator fix verified."
2. Move this task to `completed/`
3. Next: Phase 1 GA multi-tenant fix still needs manual testing with 2+ tenants

---

## If Verification Fails

1. Collect error details (screenshot, Rails logs, error message)
2. Post in chat with:
   - Error message
   - Rails log output
   - Page load time / any timeouts
3. Do NOT modify code; escalate to planning agent for investigation

---

## Environment Notes

- **Branch**: Fix is on `fix/ga-tenant-property-scoping`
- **No Code Changes Required**: Decorator is loaded automatically by config/to_prepare
- **Timeout**: If web service doesn't boot within 2 minutes, check docker logs: `docker compose logs web | head -200`

---

## Success = Ready for Next Phase

Once this verifies, both GA Phase 1 (code complete) and Wings fix (deployed) are ready for integration testing.

Next stop: Manual multi-tenant GA analytics testing with 2+ distinct tenants.
