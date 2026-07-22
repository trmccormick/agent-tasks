---
status: active
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
date_created: 2026-07-22
date_assigned: 2026-07-22
---

## ⚡ Minimal Handoff — Copy This Entire Block to Dispatch to Qwen Agent

```
You are **Implementation Agent** for Samvera Hyku GA Multi-Tenant Fix — PHASE 1 (Monkey-Patch).

PROJECT: Samvera Hyku  
TASK FILE: /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/active/2026-07-22-HIGH-BUGFIX-GA-MULTITENANT-HYKU-OVERRIDE.md  
GITHUB ISSUES: #2985 | #2995 | PALNI/PALCI #611 (production validation)  
SYNTHESIS: /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/summaries/2026-07-22-SYNTHESIS-GA-MULTITENANT-IMPLEMENTATION-OPTIONS.md  
PHASE: 1 of 2 (Urgent fix in Hyku, Phase 2 = upstream Hyrax PR)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CRITICAL CONTEXT:

Target code (Hyrax::Analytics::Ga4, analytics controllers) lives in the HYRAX GEM,
not this Hyku workspace. This task creates Hyku-specific overrides to patch it
immediately. Phase 2 will push these fixes upstream to samvera/hyrax.

STEP 1 — VERIFY BRANCH IS READY

  cd /Users/tam0013/Documents/git/hyku
  git status  # MUST show: On branch fix/ga-tenant-property-scoping
  git log --oneline -1
  
  Copy both outputs to chat. Stop if branch is wrong.

STEP 2 — READ PREREQUISITES IN THIS ORDER

  1. /Users/tam0013/Documents/git/agent-tasks/README.md (EXECUTOR Role section only)
  2. /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/README.md
  3. The synthesis report: summaries/2026-07-22-SYNTHESIS-GA-MULTITENANT-IMPLEMENTATION-OPTIONS.md
  4. This task file (all sections)

  DO NOT SKIP. All contain critical context.

STEP 3 — UNDERSTAND THE OVERRIDE APPROACH

  This task is PHASE 1 (monkey-patch in Hyku for immediate fix).
  After this completes, PHASE 2 will create a separate PR to samvera/hyrax upstream.
  
  Your job: Make multi-tenant analytics work TODAY in this Hyku instance.

STEP 4 — CREATE SYNTHESIS ADDENDUM (Quick Gate)

  This is an ADDENDUM to the synthesis report already completed. You don't need
  to create a full synthesis — just confirm in chat that you understand:
  
  ✓ Files we're modifying live in Hyrax gem (not directly editable here)
  ✓ We're creating Hyku-specific overrides using monkey-patching
  ✓ These overrides inject tenant-aware GA4 behavior into Hyrax
  ✓ After this phase, Phase 2 will upstream the fix to samvera/hyrax
  
  Post: "SYNTHESIS ADDENDUM — Phase 1 override approach understood. Ready to implement."
  WAIT for approval before proceeding.

STEP 5 — IMPLEMENT HYKU OVERRIDES

  Create override files in Hyku (not modifying Hyrax files directly).
  See "Implementation Plan" section for exact files and code.

STEP 6 — TEST IN MULTI-TENANT SETUP

  Configure 2+ test tenants with distinct GA properties.
  Verify each tenant's analytics reports are isolated (different property IDs).

STEP 7 — COMMIT & PREPARE FOR PHASE 2

  Commit all override code to fix/ga-tenant-property-scoping branch.
  After approval, Phase 2 will extract these overrides and create upstream PR to Hyrax.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CRITICAL CHECKPOINTS (stop if any fail):
  ✓ Branch verification passes (Step 1)
  ✓ Prerequisites read (Step 2)
  ✓ Synthesis addendum approved (Step 4)
  ✓ All tests pass (Step 6)
  ✓ Backward compatibility confirmed (Step 6)

QUESTIONS? Post in chat. Blockers? Document and escalate. DO NOT GUESS.
```

---

# TASK: Implement GA Multi-Tenant Override in Hyku (Phase 1 — Urgent Fix)

**Status**: ACTIVE
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-07-22
**Last Updated**: 2026-07-22
**Phase**: 1 of 2 (Monkey-patch for immediate deployment)
**GitHub Issues**: [#2985](https://github.com/samvera/hyku/issues/2985) | [#2995](https://github.com/samvera/hyku/issues/2995)
**Production Validation**: [PALNI/PALCI #611](https://github.com/notch8/palni_palci_knapsack/issues/611)
**Synthesis**: `2026-07-22-SYNTHESIS-GA-MULTITENANT-IMPLEMENTATION-OPTIONS.md`

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Agent Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section only)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/README.md`
3. **Synthesis Report**: `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/summaries/2026-07-22-SYNTHESIS-GA-MULTITENANT-IMPLEMENTATION-OPTIONS.md`
4. **This Task File**: Everything below

---

## Executive Summary

**The Problem**: Multi-tenant Hyku shows identical analytics across all tenants. Root cause: All tenants query the same GA property ID (from ENV) instead of tenant-specific IDs.

**Production Impact**: PALNI/PALCI has 248+ phantom entries per tenant, 100% data loss for one tenant.

**Solution (Phase 1 — This Task)**: 
Create Hyku-specific **override code** that patches the Hyrax GA4 service to accept tenant-specific property IDs. This provides an immediate fix in this Hyku instance.

**Phase 2 (Separate Task)**: 
Extract these overrides and create upstream PR to `samvera/hyrax` so all Hyku instances benefit.

---

## Architecture: Why Monkey-Patching

### The Challenge
The code we need to modify lives in the **Hyrax gem** (external dependency):
- `Hyrax::Analytics::Ga4` service
- `Hyrax::Admin::Analytics::*` controllers
- These files are NOT in the hyku workspace; they're in vendor/bundle at runtime

### The Solution
Create **Hyku-specific overrides** that patch Hyrax's behavior:
1. Monkey-patch `Hyrax::Analytics::Ga4` to accept `property_id` parameter
2. Override analytics controllers to get tenant property_id and pass it to Ga4
3. Load overrides in `config/initializers/`

### Why This Approach
- ✅ Immediate fix (no waiting for Hyrax maintainers)
- ✅ Self-contained in Hyku repo
- ✅ Easy to remove after Hyrax is updated (Phase 2)
- ✅ No code duplication (uses module prepend, not full copy-paste)

### Limitation
- ❌ Only fixes this Hyku instance
- ❌ Other multi-tenant Hyku deployments still affected
- ❌ That's why Phase 2 exists: upstream PR to fix it for everyone

---

## Implementation Plan

### File Structure (New Files)

```
lib/hyku/
  ├── analytics/
  │   ├── ga4_tenant_override.rb          # Patches Hyrax::Analytics::Ga4
  │   └── controllers_tenant_override.rb  # Patches analytics controllers
  └── initializers/
      └── ga4_tenant_patch.rb             # Loads overrides on startup

spec/lib/hyku/analytics/
  ├── ga4_tenant_override_spec.rb
  └── controllers_tenant_override_spec.rb
```

### 1. Create Override for Ga4 Service

**File**: `lib/hyku/analytics/ga4_tenant_override.rb`

```ruby
# frozen_string_literal: true

module Hyku
  module Analytics
    # Patches Hyrax::Analytics::Ga4 to support tenant-specific property IDs
    # This allows multi-tenant Hyku to isolate analytics by tenant.
    # Phase 2: These changes will be contributed upstream to samvera/hyrax
    module Ga4TenantOverride
      def initialize(property_id: nil, property_name: nil)
        @custom_property_id = property_id
        @property_name = property_name
      end

      def property
        property_id = @custom_property_id || config.property_id
        "properties/#{property_id}"
      end
    end
  end
end

# Apply override: prepend this module to Hyrax::Analytics::Ga4
Hyrax::Analytics::Ga4.prepend(Hyku::Analytics::Ga4TenantOverride)
```

**Why `prepend`**: Prepend adds this module to the lookup chain BEFORE the original Ga4 methods, so our initialize and property methods override Hyrax's.

### 2. Create Override for Collection Reports Controller

**File**: `lib/hyku/analytics/controllers_tenant_override.rb`

```ruby
# frozen_string_literal: true

module Hyku
  module Analytics
    # Patches Hyrax analytics controllers to use tenant-specific GA property IDs
    # Ensures each tenant's analytics report only includes their own GA data
    module ControllersTenantOverride
      def initialize_analytics(property_id: nil)
        property_id = property_id || 
                      current_account.google_analytics_property_id || 
                      ENV.fetch('GOOGLE_ANALYTICS_PROPERTY_ID', '')
        Hyrax::Analytics::Ga4.new(property_id: property_id)
      end

      private :initialize_analytics
    end
  end
end

# Apply override to both analytics controllers
Hyrax::Admin::Analytics::CollectionReportsController.include(Hyku::Analytics::ControllersTenantOverride)
Hyrax::Admin::Analytics::WorkReportsController.include(Hyku::Analytics::ControllersTenantOverride)
```

Then modify controllers to use the helper (see below).

### 3. Update Controllers to Use Helper

**File**: `app/controllers/hyku/admin/analytics/collection_reports_controller_override.rb`

```ruby
# frozen_string_literal: true

module Hyku
  module Admin
    module Analytics
      class CollectionReportsControllerOverride < Hyrax::Admin::Analytics::CollectionReportsController
        def index
          tenant_property_id = current_account.google_analytics_property_id || ENV.fetch('GOOGLE_ANALYTICS_PROPERTY_ID', '')
          @analytics = Hyrax::Analytics::Ga4.new(property_id: tenant_property_id)
          @all_top_collections = @analytics.top_collections_for_deposit
          # ... rest of original method
        end
      end
    end
  end
end
```

**Alternative**: Use monkey-patch prepend instead (see initializer below).

### 4. Initializer to Load Overrides

**File**: `config/initializers/ga4_tenant_patch.rb`

```ruby
# frozen_string_literal: true

# Load Hyku GA4 tenant-aware overrides
# These patches enable multi-tenant analytics isolation by allowing
# each tenant to use their own Google Analytics property ID.
#
# Phase 2: These changes will be contributed upstream to samvera/hyrax
# After upstream merge, this initializer can be removed.

require 'hyku/analytics/ga4_tenant_override'
require 'hyku/analytics/controllers_tenant_override'

# Note: Overrides are applied at load time when modules are required.
# See the respective override files for prepend/include statements.
```

---

## Testing Strategy

### 1. Unit Tests for Ga4 Override

**File**: `spec/lib/hyku/analytics/ga4_tenant_override_spec.rb`

```ruby
require 'rails_helper'

RSpec.describe 'Ga4 Tenant Override', type: :lib do
  describe 'Hyrax::Analytics::Ga4 with tenant property_id' do
    it 'accepts custom property_id parameter' do
      ga4 = Hyrax::Analytics::Ga4.new(property_id: 'tenant-123')
      expect(ga4.property).to eq('properties/tenant-123')
    end

    it 'falls back to ENV property_id when not provided' do
      allow(ENV).to receive(:fetch).with('GOOGLE_ANALYTICS_PROPERTY_ID', '').and_return('env-456')
      ga4 = Hyrax::Analytics::Ga4.new
      # Verify fallback (depends on Ga4 config)
    end

    it 'prioritizes custom property_id over ENV' do
      allow(ENV).to receive(:fetch).with('GOOGLE_ANALYTICS_PROPERTY_ID', '').and_return('env-456')
      ga4 = Hyrax::Analytics::Ga4.new(property_id: 'custom-789')
      expect(ga4.property).to eq('properties/custom-789')
    end
  end
end
```

### 2. Integration Tests for Multi-Tenant Analytics

**File**: `spec/lib/hyku/analytics/controllers_tenant_override_spec.rb`

```ruby
require 'rails_helper'

RSpec.describe 'Multi-Tenant Analytics Override', type: :request do
  describe 'Collection Reports with tenant GA property' do
    it 'uses tenant property_id from Account' do
      account_a = create(:account, 
        cname: 'tenant-a',
        google_analytics_property_id: 'property-a-111'
      )
      account_b = create(:account,
        cname: 'tenant-b',
        google_analytics_property_id: 'property-b-222'
      )

      # Login as tenant-a, verify it uses property-a-111
      allow_any_instance_of(Hyrax::Admin::Analytics::CollectionReportsController)
        .to receive(:current_account).and_return(account_a)
      
      get analytics_admin_collection_reports_path, host: 'tenant-a.localhost'
      
      # Verify GA4 was initialized with correct property_id
      # (implementation depends on how GA4 is mocked)
    end

    it 'falls back to ENV when Account property_id not set' do
      account = create(:account, cname: 'tenant-c', google_analytics_property_id: nil)
      allow(ENV).to receive(:fetch).with('GOOGLE_ANALYTICS_PROPERTY_ID', '').and_return('env-default')

      get analytics_admin_collection_reports_path
      # Verify GA4 was initialized with ENV value
    end
  end
end
```

### 3. Manual Multi-Tenant Testing

**Setup**:
1. Create 2+ tenants locally with distinct GA properties:
   - Tenant A: `google_analytics_property_id = '111111111'`
   - Tenant B: `google_analytics_property_id = '222222222'`

2. Generate test analytics data in each tenant

3. **Verify**:
   - Login to Tenant A, check Collections Report
   - Record: Top collections, counts, entries
   - Login to Tenant B, check Collections Report
   - Verify: Different data than Tenant A (or same collections with different counts)
   - No overlap of collection IDs between tenants

---

## Code Quality Notes

- **Module Prepend vs Include**: Use `prepend` for Ga4 (modifies method behavior), `include` for controllers (adds helper methods)
- **Comments**: Explain tenant scoping at each modification point + reference to Phase 2
- **Error Handling**: Use `ENV.fetch` with default, don't crash if Account field is nil
- **Backward Compatible**: Single-tenant deployments still work (ENV fallback)
- **No Copy-Paste**: Use module overrides, not duplicated Hyrax code

---

## Acceptance Criteria

✓ **Phase 1 Goal**: Multi-tenant analytics work correctly in this Hyku instance  
✓ **Tenant Isolation**: Each tenant queries only their own GA property  
✓ **RSpec Tests**: Unit and integration tests verify tenant isolation  
✓ **Manual Testing**: 2+ test tenants show different analytics data  
✓ **Backward Compatible**: Single-tenant deployments unaffected  
✓ **Code Ready for Phase 2**: Override code is clean and extractable  

---

## What Happens After Phase 1

After this implementation is approved and tested:

1. **Phase 2 Task**: Create upstream PR to `samvera/hyrax` with these changes
2. **Hyrax Review**: Samvera maintainers review and merge
3. **Hyrax Release**: Hyrax releases new version with fix
4. **Hyku Bump**: Hyku updates Gemfile to use new Hyrax version
5. **Remove Overrides**: Delete these override files (Phase 1 temporary code) once Hyrax is updated

---

## Gotchas & Architecture Notes

### 1. Prepend vs Include
- **`prepend`**: For overriding instance methods in Ga4 (initialize, property)
- **`include`**: For adding helper methods to controllers

### 2. Account Model Column
Verify `google_analytics_property_id` column exists:
```bash
rails c
Account.first.attributes.keys.include?('google_analytics_property_id')
# Should return true
```

If missing, create migration (unlikely, but handle gracefully with nil-check).

### 3. ENV Fallback
Always fallback to ENV if Account field is nil:
```ruby
property_id = current_account.google_analytics_property_id || ENV.fetch('GOOGLE_ANALYTICS_PROPERTY_ID', '')
```

### 4. Monkey-Patching Risk
This override only works if Hyrax's Ga4 and controller structures don't change radically. If Hyrax refactors heavily, this override may break. **That's OK** — Phase 2 fixes it upstream.

---

## Estimated Work Breakdown

- **Override Code**: 1-1.5 hours
- **RSpec Tests**: 1 hour
- **Manual Testing**: 30-45 minutes
- **Verification**: 30 minutes

**Total**: ~3-4 hours (same as Phase 1 upstream, but this is Phase 1 of 2)

---

## Branch & Git Workflow

**Branch**: `fix/ga-tenant-property-scoping` (already created)  
**All work on this branch** until Phase 2 PR is created

---

## After Implementation

1. Commit all override code to feature branch
2. Post implementation summary to chat
3. Phase 2 task will extract these changes and create Hyrax PR
4. After Phase 2 PR approved, merge this Phase 1 branch

---

## Success Criteria (for Task Completion)

1. ✅ All override files created and loading correctly
2. ✅ RSpec tests pass (unit + integration)
3. ✅ Manual multi-tenant testing confirms tenant isolation
4. ✅ Code is documented and clear (for Phase 2 extraction)
5. ✅ Task moved to `completed/`

---

## Contact & Escalation

- **Questions**: Post in task chat
- **Blockers**: Document and escalate
- **Scope Changes**: Do NOT implement, escalate to planning agent
