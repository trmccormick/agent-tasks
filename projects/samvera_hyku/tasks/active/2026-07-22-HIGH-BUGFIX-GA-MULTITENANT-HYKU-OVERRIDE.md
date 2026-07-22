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

STEP 3 — UNDERSTAND THE CORRECTED ARCHITECTURE

  CRITICAL FINDING: Ga4 is a MODULE with class methods, not a class.
  Controllers call Hyrax::Analytics.daily_events() (class methods).
  There's no .new() instantiation happening.
  
  Read corrected synthesis: summaries/2026-07-22-CORRECTED-SYNTHESIS-ACTUAL-GA4-ARCHITECTURE.md
  
  This changes implementation from instance method override to controller override.
  Much simpler approach: set config.property_id before calling analytics methods.
  
  Your job: Make multi-tenant analytics work TODAY in this Hyku instance.

STEP 4 — CONFIRM CORRECTED APPROACH

  Post to chat:
  "Reviewed corrected synthesis. Understand that Ga4 is module-based with class
   methods. Controller override approach (Option A) is cleaner. Ready to implement."
  
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

**Why Only Hyku?**: Hyrax is single-tenant, so this issue only affects **multi-tenant Hyku deployments**. The fix belongs in Hyku, not in Hyrax.

**Solution (This Task — Permanent Fix)**: 
Create Hyku-specific **override code** that patches the Hyrax GA4 service to accept tenant-specific property IDs. This is the **permanent solution** for multi-tenant Hyku instances.

**Phase 2 (Separate Task)**: 
After Phase 1 is working, create an **issue in samvera/hyrax** proposing an optional `property_id` parameter (for flexibility). This is a discussion with maintainers, not an implementation.

---This Is the Right Place for the Fix

### The Challenge
The code we need to modify lives in the **Hyrax gem** (external dependency):
- `Hyrax::Analytics::Ga4` service
- `Hyrax::Admin::Analytics::*` controllers
- These files are NOT in the hyku workspace; they're in vendor/bundle at runtime

### Why Hyrax Can't Solve This
- Hyrax is **single-tenant** — it doesn't have the concept of `current_account` or tenant-specific properties
- Multi-tenancy is a **Hyku feature**, not a Hyrax feature
- The fix is **Hyku-specific**, not a Hyrax improvement

### The Solution (Permanent Fix in Hyku)
Create **Hyku-specific overrides** that patch Hyrax's behavior:
1. Monkey-patch `Hyrax::Analytics::Ga4` to accept `property_id` parameter
2. Override analytics controllers to get tenant property_id and pass it to Ga4
3. Load overrides in `config/initializers/`

### Why This Approach
- ✅ **Permanent solution** for multi-tenant Hyku
- ✅ Self-contained in Hyku repo (correct architectural location)
- ✅ No code duplication (uses module prepend, not full copy-paste)
- ✅ Solves the problem at the multi-tenant layer where it belongs
- ✅ Easy to maintain (clearly marked as Hyku extensions to Hyrax)

### Note
- Other multi-tenant Hyku instances (like PALNI/PALCI) can use the same pattern
- Phase 2 will document this pattern + propose optional flexibility to Hyrax maintainers
- ❌ Other multi-tenant Hyku deployments still affected
- ❌ That's why Phase 2 exists: upstream PR to fix it for everyone

---

## Implementation Plan (CORRECTED FOR ACTUAL GA4 ARCHITECTURE)

### Key Discovery: Ga4 Is Module-Based (Not Instance-Based)

**What We Found**:
- `Hyrax::Analytics::Ga4` is a **module** with **class methods**, not a class
- Controllers call `Hyrax::Analytics.daily_events()` (class methods)
- Config is accessed via `Hyrax::Analytics.config.property_id`
- No `.new()` instantiation happens in controllers

**Simpler Implementation**: Override controllers to temporarily set tenant property_id before calling analytics methods.

### File Structure (New Files)

```
lib/hyku/
  └── analytics/
      └── tenant_aware_analytics.rb              # Helper module for tenant-aware config

app/controllers/hyku/admin/analytics/
  ├── collection_reports_controller.rb           # Override collection reports
  └── work_reports_controller.rb                 # Override work reports

spec/lib/hyku/analytics/
  └── tenant_aware_analytics_spec.rb             # Unit tests

spec/controllers/hyku/admin/analytics/
  └── collection_reports_controller_spec.rb      # Integration tests
```

### 1. Create Tenant-Aware Analytics Helper

**File**: `lib/hyku/analytics/tenant_aware_analytics.rb`

```ruby
# frozen_string_literal: true

module Hyku
  module Analytics
    # Provides tenant-aware analytics for multi-tenant Hyku
    # Sets tenant-specific GA property_id before calling Hyrax analytics methods
    # Restores original property_id after the block completes
    module TenantAwareAnalytics
      private

      def with_tenant_analytics
        # Get tenant-specific property ID (fallback to ENV)
        tenant_property_id = current_account.google_analytics_property_id || 
                             ENV.fetch('GOOGLE_ANALYTICS_PROPERTY_ID', '')
        
        # Save original config property_id
        original_property_id = Hyrax::Analytics.config.property_id
        
        # Set tenant-aware property_id
        Hyrax::Analytics.config.property_id = tenant_property_id
        
        begin
          yield
        ensure
          # Always restore original (important for tests and request isolation)
          Hyrax::Analytics.config.property_id = original_property_id
        end
      end
    end
  end
end
```

**How it works**:
1. Save the original GA property_id
2. Set Hyrax::Analytics.config.property_id to tenant's property_id
3. Execute the block (which calls analytics methods)
4. Restore the original property_id

### 2. Override Collection Reports Controller

**File**: `app/controllers/hyku/admin/analytics/collection_reports_controller.rb`

```ruby
# frozen_string_literal: true

module Hyku
  module Admin
    module Analytics
      class CollectionReportsController < Hyrax::Admin::Analytics::CollectionReportsController
        include Hyku::Analytics::TenantAwareAnalytics

        # Override index to use tenant-aware analytics
        def index
          return unless Hyrax.config.analytics_reporting?
          
          with_tenant_analytics do
            super
          end
        end

        # Override show to use tenant-aware analytics
        def show
          return unless Hyrax.config.analytics_reporting?
          
          with_tenant_analytics do
            super
          end
        end
      end
    end
  end
end
```

### 3. Override Work Reports Controller

**File**: `app/controllers/hyku/admin/analytics/work_reports_controller.rb`

```ruby
# frozen_string_literal: true

module Hyku
  module Admin
    module Analytics
      class WorkReportsController < Hyrax::Admin::Analytics::WorkReportsController
        include Hyku::Analytics::TenantAwareAnalytics

        # Override index to use tenant-aware analytics
        def index
          return unless Hyrax.config.analytics_reporting?
          
          with_tenant_analytics do
            super
          end
        end

        # Override show to use tenant-aware analytics
        def show
          with_tenant_analytics do
            super
          end
        end
      end
    end
  end
end
```

### 4. Load Overrides in Routes (if needed)

If Hyku doesn't automatically load controllers from `app/controllers/hyku/`, add to `config/routes.rb`:

```ruby
namespace :hyku do
  namespace :admin do
    namespace :analytics do
      # Override Hyrax analytics controllers
      resources :collection_reports, only: [:index, :show]
      resources :work_reports, only: [:index, :show]
    end
  end
end
```

Or mount them in `config/initializers/`:  
**File**: `config/initializers/99_hyku_analytics_overrides.rb`

```ruby
# frozen_string_literal: true

# Load Hyku analytics controller overrides
# These patches enable multi-tenant analytics isolation by allowing
# each tenant to use their own Google Analytics property ID.
#
# Phase 2: These changes will be contributed upstream to samvera/hyrax
# After upstream merge, this initializer can be removed.

# The controller overrides are in app/controllers/hyku/admin/analytics/
# They automatically inherit from Hyrax controllers and override index/show methods
```

---

## Testing Strategy

### 1. Unit Tests for Tenant-Aware Analytics Helper

**File**: `spec/lib/hyku/analytics/tenant_aware_analytics_spec.rb`

```ruby
require 'rails_helper'

RSpec.describe Hyku::Analytics::TenantAwareAnalytics do
  describe '#with_tenant_analytics' do
    let(:dummy_class) { Class.new { include Hyku::Analytics::TenantAwareAnalytics } }
    let(:controller) { dummy_class.new }

    before do
      # Mock current_account
      allow(controller).to receive(:current_account).and_return(account)
      # Mock Hyrax::Analytics.config
      allow(Hyrax::Analytics).to receive(:config).and_return(config)
    end

    context 'when account has property_id' do
      let(:account) { double(google_analytics_property_id: 'tenant-property-111') }
      let(:config) { double(property_id: 'original-env-id') }

      it 'sets tenant property_id during execution' do
        allow(config).to receive(:property_id=)
        allow(config).to receive(:property_id).and_return('tenant-property-111', 'original-env-id')

        controller.send(:with_tenant_analytics) do
          expect(config).to have_received(:property_id=).with('tenant-property-111')
        end
      end

      it 'restores original property_id after execution' do
        expect(config).to receive(:property_id=).with('tenant-property-111')
        expect(config).to receive(:property_id=).with('original-env-id')

        controller.send(:with_tenant_analytics) { nil }
      end
    end

    context 'when account has no property_id' do
      let(:account) { double(google_analytics_property_id: nil) }
      let(:config) { double(property_id: 'original-env-id') }

      it 'falls back to ENV value' do
        allow(ENV).to receive(:fetch).with('GOOGLE_ANALYTICS_PROPERTY_ID', '').and_return('env-fallback')
        allow(config).to receive(:property_id=)

        controller.send(:with_tenant_analytics) do
          expect(config).to have_received(:property_id=).with('env-fallback')
        end
      end
    end

    context 'when exception occurs in block' do
      let(:account) { double(google_analytics_property_id: 'tenant-id') }
      let(:config) { double(property_id: 'original-id') }

      it 'still restores original property_id' do
        expect(config).to receive(:property_id=).with('original-id')

        expect {
          controller.send(:with_tenant_analytics) { raise 'error' }
        }.to raise_error('error')
      end
    end
  end
end
```

### 2. Integration Tests for Collection Reports Controller

**File**: `spec/controllers/hyku/admin/analytics/collection_reports_controller_spec.rb`

```ruby
require 'rails_helper'

RSpec.describe Hyku::Admin::Analytics::CollectionReportsController do
  let(:tenant_a) { create(:account, cname: 'tenant-a', google_analytics_property_id: '111111111') }
  let(:tenant_b) { create(:account, cname: 'tenant-b', google_analytics_property_id: '222222222') }
  
  describe '#index with multi-tenant analytics' do
    it 'uses tenant property_id for tenant A' do
      allow(controller).to receive(:current_account).and_return(tenant_a)
      allow(controller).to receive(:current_user).and_return(build_stubbed(:user, admin: true))
      allow(Hyrax.config).to receive(:analytics_reporting?).and_return(true)

      get :index
      
      # Verify the controller called with_tenant_analytics (property_id should be tenant_a's)
      expect(response).to be_successful
      # Additional verification: Check that Hyrax::Analytics.config.property_id was set correctly
      # (Would require additional mocking/instrumentation)
    end

    it 'uses different property_id for tenant B' do
      allow(controller).to receive(:current_account).and_return(tenant_b)
      allow(controller).to receive(:current_user).and_return(build_stubbed(:user, admin: true))
      allow(Hyrax.config).to receive(:analytics_reporting?).and_return(true)

      get :index
      
      expect(response).to be_successful
      # Verify tenant_b's property_id was used (different from tenant_a)
    end
  end
end
```

### 3. Manual Multi-Tenant Testing

**Setup**:
1. Create 2 test tenants in local environment with distinct GA properties:
   ```bash
   # In Rails console
   account_a = Account.create!(name: 'Test Tenant A', cname: 'tenant-a.localhost')
   account_a.update!(google_analytics_property_id: '111111111')
   
   account_b = Account.create!(name: 'Test Tenant B', cname: 'tenant-b.localhost')
   account_b.update!(google_analytics_property_id: '222222222')
   ```

2. Enable analytics in both accounts (if required)

3. **Verify Isolation**:
   - Login as admin in Tenant A
   - Navigate to Analytics → Collections Report
   - Record visible collections and counts
   - Logout, login as admin in Tenant B
   - Navigate to Analytics → Collections Report
   - Verify: Different collection data than Tenant A
   - Verify: No cross-tenant collection IDs visible
   - Test repeated: Switch back to Tenant A, verify data is consistent

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

### Phase 1 Is The Permanent Solution

This code is **NOT temporary**. It is the correct, permanent solution for multi-tenant GA support in Hyku because:
- Multi-tenancy is a **Hyku feature**, not a Hyrax feature
- This override belongs at the multi-tenant layer
- Single-tenant Hyrax deployments don't need this
- Multi-tenant Hyku instances can use this pattern

### Phase 2: Optional Proposal (Discussion, Not Implementation)

After Phase 1 is working, we'll create a GitHub discussion in `samvera/hyrax` proposing:

> "Would Hyrax consider an optional `property_id` parameter in the Ga4 service?"

**Possible Outcomes**:
- ✅ If Hyrax says YES: Future Hyrax versions could support this natively, simplifying Hyku's override
- ✅ If Hyrax says NO: No problem — Hyku keeps this override (architecturally correct for multi-tenancy)
- ✅ Either way: This Phase 1 code remains valid and working

**The Key Point**: Phase 1 is **done and permanent**. Phase 2 is a **background discussion** with no urgency.

---

## Gotchas & Mitigation (Corrected for Module-Based Ga4)

### 1. Ga4 Is Module-Based (Not Instance-Based)
**Gotcha**: Task file originally assumed instance methods, but Ga4 uses class methods  
**Mitigation**: ✅ Now using controller overrides that set/restore `config.property_id`  
**Impact**: Simpler implementation, cleaner code, easier to test

### 2. Config Is Mutable and Shared
**Gotcha**: `Hyrax::Analytics.config.property_id` is shared globally; setting it affects all analytics calls  
**Mitigation**: Use `ensure` block to restore original value after each request  
**Implementation**:
```ruby
original_property_id = Hyrax::Analytics.config.property_id
Hyrax::Analytics.config.property_id = tenant_property_id
begin
  yield  # Call analytics methods with tenant property_id
ensure
  Hyrax::Analytics.config.property_id = original_property_id
end
```

### 3. Current Account May Be Nil
**Gotcha**: Anonymous users don't have `current_account`, causing nil error  
**Mitigation**: Use safe navigation operator and ENV fallback  
**Implementation**:
```ruby
tenant_property_id = current_account&.google_analytics_property_id || 
                     ENV.fetch('GOOGLE_ANALYTICS_PROPERTY_ID', '')
```

### 4. Account Column Must Exist
**Gotcha**: If `google_analytics_property_id` column doesn't exist, controller crashes  
**Mitigation**: Verify in AccountSettings concern (already exists in Hyku)  
**Verification**:
```bash
rails c
Account.first.google_analytics_property_id  # Should return string or nil, not error
```

### 5. Request Isolation in Tests
**Gotcha**: Tests may leak state if they don't clean up config.property_id  
**Mitigation**: Use `ensure` block in with_tenant_analytics + reset in test teardown  
**Test Pattern**:
```ruby
after do
  # Reset config to original value
  Hyrax::Analytics.config.property_id = @original_property_id
end
```

### 6. Ga4 Client Caching
**Gotcha**: Ga4 caches the client instance (`@client ||=`); if config changes, cache isn't invalidated  
**Mitigation**: ✅ This isn't a problem — we set property_id BEFORE calling analytics methods  
**Why**: Each request gets fresh property_id, and Ga4 methods use current config when querying

### 7. Backward Compatibility (Single-Tenant)
**Gotcha**: Single-tenant deployments might break if Account field is nil and ENV is not set  
**Mitigation**: Always use `ENV.fetch(..., '')` with default value  
**Verification**: Single-tenant test should verify analytics still work without current_account

---

## Estimated Work Breakdown

- **Override Code**: 1 hour
- **RSpec Tests**: 1 hour
- **Manual Testing**: 45 minutes
- **Verification**: 30 minutes

**Total**: ~3-3.5 hours

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
