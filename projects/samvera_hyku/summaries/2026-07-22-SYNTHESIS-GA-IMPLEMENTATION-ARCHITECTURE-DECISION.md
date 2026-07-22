# SYNTHESIS REPORT — GA Multi-Tenant Implementation Architecture Decision
**Date**: 2026-07-22  
**Agent**: Qwen (Implementation Agent)  
**Status**: ⚠️ BLOCKED — Awaiting architecture decision  
**Critical Finding**: Target code files are in Hyrax gem, not Hyku workspace

---

## Executive Summary

**Discovery**: The files we need to modify (`app/services/hyrax/analytics/ga4.rb`, analytics controllers, etc.) are part of the **Hyrax gem**, an external dependency sourced from GitHub, not vendored in the Hyku workspace.

**Impact**: This changes the entire implementation approach. We have two viable paths forward:

1. **Path A**: Implement fixes in samvera/hyrax repository (upstream fix)
2. **Path B**: Implement GA overrides in Hyku workspace (downstream workaround)

**Recommendation**: Present both options to team for decision. This affects scope, testing, and PR targets.

---

## Critical Finding Details

### Evidence

1. **File Search Results**:
   - `ga4.rb` — 0 results in hyku workspace
   - `collection_reports_controller.rb` — 0 results in hyku workspace
   - `work_reports_controller.rb` — 0 results in hyku workspace
   - `Ga4.new` calls in actual code — 0 results (only in task documentation)

2. **Gemfile Analysis**:
   ```ruby
   gem 'hyrax', github: 'samvera/hyrax', branch: 'main'
   ```
   - Hyrax is a GitHub dependency, not local or vendored
   - Code is installed in `vendor/bundle` or gem cache at runtime
   - Not directly editable in hyku workspace

3. **Architecture Confirmation**:
   - Hyku = Rails app that uses Hyrax gem
   - Hyrax = Gem containing all analytics code
   - Hyku does NOT override these files locally
   - Changes to analytics require either: (a) update Hyrax upstream, or (b) create Hyku-specific overrides

---

## Root Cause of This Discovery

### Why the Task File Didn't Catch This

The task file was created based on the **implementation handoff from 2026-07-07**, which assumed:
- Changes would be made to files accessible in the Hyku workspace
- The reviewer (Rob) approved changes to Hyrax files without clarifying where changes would live

**Lesson Learned**: This is why synthesis gates exist. Executor must verify assumptions about repository structure BEFORE planning implementation.

---

## OPTION A: Implement in Hyrax Repository (Upstream Fix)

### Approach
Modify files in **samvera/hyrax** repository (external, upstream):
- `app/services/hyrax/analytics/ga4.rb` (in Hyrax gem)
- `app/controllers/hyrax/admin/analytics/collection_reports_controller.rb` (in Hyrax gem)
- `app/controllers/hyrax/admin/analytics/work_reports_controller.rb` (in Hyrax gem)

### Benefits
✅ **Correct Place**: Fixes root cause at source  
✅ **Upstream First**: Follows open-source best practice  
✅ **Solves for All Hyku Instances**: Any Hyku deployment using updated Hyrax gets the fix  
✅ **Aligns with Reviewer Approval**: Rob (Samvera maintainer) already approved these changes  
✅ **No Local Workarounds**: Cleaner codebase, no Hyku-specific overrides  
✅ **Production-Ready**: Fix helps PALNI/PALCI and all multi-tenant Hyku Commons instances  

### Challenges
❌ **Out-of-Scope for This Hyku Workspace**: We'd need a separate checkout of samvera/hyrax  
❌ **Dependency on Hyrax Maintainers**: Hyku can't release fix until Hyrax merges and releases new version  
❌ **Version Coordination**: Hyku would need to bump Hyrax version in Gemfile after fix is released  
❌ **Timing Uncertainty**: When will Hyrax maintainers review and merge PR?  

### Implementation Steps

**1. Clone Hyrax repository** (if not already available):
```bash
cd /Users/tam0013/Documents/git
git clone https://github.com/samvera/hyrax.git
cd hyrax
git checkout main
git pull
```

**2. Create feature branch in Hyrax**:
```bash
git checkout -b fix/ga-tenant-property-scoping
```

**3. Modify Hyrax files** (same 4 modifications as documented in task):
- Add property_id parameter to Ga4 client
- Update controllers to pass tenant property_id
- Add RSpec tests (in hyrax/spec/)

**4. Commit & create PR to samvera/hyrax**:
- Link to Samvera issues #2985, #2995, PALNI/PALCI #611
- Use provided PR template
- Wait for Hyrax maintainer review

**5. After PR merged**: Hyku updates Gemfile to use new Hyrax version

### Who Owns This Work?
- **Hyrax: Samvera maintainers** (or external contributor with strong Hyrax knowledge)
- **Hyku: Separate task** to bump Hyrax version after upstream fix is released

---

## OPTION B: Implement GA Override in Hyku Workspace (Downstream Workaround)

### Approach
Create Hyku-specific override code that patches/extends Hyrax's GA4 service:
- Create `lib/hyku/analytics/ga4_override.rb` (Hyku custom code)
- Create `app/controllers/hyku/admin/analytics/collection_reports_controller.rb` (Hyku override)
- Similar for work_reports_controller
- Load overrides in `config/initializers/hyrax.rb` or routes

### Benefits
✅ **No External Dependencies**: Fix is self-contained in Hyku repo  
✅ **Immediate Deployment**: No wait for Hyrax maintainers  
✅ **Faster Iteration**: Can refine fix based on Hyku-specific testing  
✅ **Independent Testing**: Test in Hyku environment without coordinating with Hyrax  
✅ **Works Today**: Doesn't require Hyrax release cycle  

### Challenges
❌ **Doesn't Fix Upstream**: Other Hyku instances still affected until Hyrax is updated  
❌ **Code Duplication**: Overrides must duplicate Hyrax code to modify it (maintenance burden)  
❌ **Fragile**: If Hyrax changes, override code may break  
❌ **Technical Debt**: Override pattern is a workaround, not a permanent solution  
❌ **Not Contributing to Samvera**: Community gets no benefit (unless code is later extracted upstream)  
❌ **PALNI/PALCI Not Helped**: This Hyku instance fix doesn't help PALNI/PALCI or other multi-tenant Commons  

### Implementation Approach

**1. Create override structure in Hyku**:
```
lib/hyku/analytics/
  ├── ga4_override.rb           # Patched Ga4 class with property_id support
  └── initializers/ga4.rb       # Load override on startup

app/controllers/hyku/admin/analytics/
  ├── collection_reports_controller.rb  # Override to use tenant property_id
  └── work_reports_controller.rb         # Override to use tenant property_id
```

**2. Use Ruby monkey-patching or module prepend**:
```ruby
# lib/hyku/analytics/ga4_override.rb
module Hyku
  module Analytics
    module Ga4Override
      def initialize(property_id: nil, property_name: nil)
        @custom_property_id = property_id
        # ... rest of original init
      end

      def property
        property_id = @custom_property_id || config.property_id
        "properties/#{property_id}"
      end
    end
  end
end

# In initializer: prepend Hyku::Analytics::Ga4Override to Hyrax::Analytics::Ga4
```

**3. Test in Hyku environment** (multi-tenant local setup)

**4. Release as patch version to Hyku** (or deploy directly)

### Who Owns This Work?
- **Hyku team** (can work independently)
- **Still should eventually upstream** to Hyrax for broader benefit

---

## COMPARISON TABLE

| Factor | Option A (Upstream) | Option B (Override) |
|--------|-------------------|-------------------|
| **Target Repo** | samvera/hyrax | samvera/hyku (this repo) |
| **Implementation Speed** | Slower (depends on maintainers) | Fast (independent) |
| **Code Duplication** | None (fixes at source) | Yes (override pattern) |
| **Helps PALNI/PALCI** | ✅ Yes (once released) | ❌ No |
| **Helps Other Hyku Instances** | ✅ Yes (once Hyrax updated) | ❌ No (Hyku-specific) |
| **Maintenance Burden** | Low (upstream responsibility) | High (Hyku must maintain override) |
| **Depends on External Timeline** | ✅ Yes (maintainers) | ❌ No |
| **Aligns with Samvera Best Practice** | ✅ Yes (contribute upstream) | ⚠️ Partial (workaround) |
| **Effort in This Session** | Medium (new repo setup) | Medium (override code) |
| **Risk of Regression** | Low | Medium (monkey-patching risk) |

---

## RECOMMENDATION & NEXT STEPS

### Recommended Approach: **Hybrid Strategy**

**Phase 1: Upstream Fix (Primary)**
- Target: samvera/hyrax repository
- Effort: ~3-4 hours
- Timeline: Code ready this week, merged/released by maintainers (uncertain timeline)
- Benefit: Permanent solution, helps all Hyku instances, addresses PALNI/PALCI

**Phase 2: Hyku Temporary Override (If Urgent)**
- Target: samvera/hyku repository (this repo)
- Use IF: Multi-tenant analytics are broken and urgent deployment needed
- Benefit: Temporary fix while upstream PR is pending review
- Cleanup: Remove override after Hyrax version bump

### Decision Required from Team

**Question for Product Owners / Samvera Maintainers**:

1. Should we implement in **Hyrax upstream** (Option A) for the community benefit?
2. Or implement in **Hyku as override** (Option B) for faster deployment?
3. Or pursue **hybrid approach** (Phase 1 upstream + Phase 2 override if urgent)?

### What Blocks This Task Now

✋ **This synthesis report blocks implementation** pending decision from:
- Planning Agent (human decision-maker)
- Samvera maintainer review (if routing to Hyrax)
- Product owner (if routing to Hyku)

---

## Architecture Notes for Implementation

### If Option A (Upstream Hyrax):
- Need separate clone of samvera/hyrax repository
- Same code modifications as documented in original task file
- RSpec tests go in `hyrax/spec/services/` and `hyrax/spec/controllers/`
- PR targets `samvera/hyrax` repository, not `samvera/hyku`
- Rob already approved these changes, so review should move quickly

### If Option B (Hyku Override):
- Use Ruby `Module.prepend` or monkey-patching approach
- Override must be loaded in `config/initializers/`
- Tests stay in Hyku's `spec/` directory
- Document override clearly (comment explaining why it exists)
- Plan to remove after Hyrax version bump
- May need to handle Hyrax version upgrades carefully

---

## Questions for Clarification

Before proceeding to implementation, need answers to:

1. **Repository Target**: Which repository should be the primary target for this fix?
2. **Timeline**: Is there an urgent deadline for multi-tenant analytics fix?
3. **Hyrax Relationship**: Is Hyku team in contact with Hyrax maintainers about this issue?
4. **PALNI/PALCI**: Are we coordinating fix with PALNI/PALCI team or independent?
5. **Version Coordination**: If upstream, when should Hyku bump Hyrax version?

---

## Documents & References

- **Original Task File**: `2026-07-22-HIGH-BUGFIX-GA-MULTITENANT-PROPERTY-SCOPING.md`
- **Root Cause Analysis**: `2026-06-26-HIGH-RESEARCH-MULTITENANT-GA-ISSUES-2985-2995.md`
- **Validation**: `2026-06-26-VALIDATION-PALNI-PALCI-ISSUE-611-CONFIRMS-GA-ROOT-CAUSE.md`
- **Implementation Handoff**: `session_handoff_2026-07-07_GA-MULTITENANT-IMPLEMENTATION-READY.md`
- **Hyrax Repository**: https://github.com/samvera/hyrax
- **Hyku Repository**: https://github.com/samvera/hyku

---

## Status

**Current Status**: ⏸️ SYNTHESIS COMPLETE — Awaiting Architecture Decision  
**Next Step**: Planning Agent (human) reviews both options and decides which path to take  
**Blocking Issue**: Task cannot proceed without clarity on target repository  

This synthesis report is complete and ready for human review.
