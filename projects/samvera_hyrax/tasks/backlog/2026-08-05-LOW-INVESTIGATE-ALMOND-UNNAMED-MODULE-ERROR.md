---
id: almond-unnamed-module-2026-08-05
project: samvera_hyrax
title: "Investigate and fix almond-rails unnamed module error in asset pipeline"
severity: LOW
priority: deferred
category: upstream-bug-fix
related_issues: []
discovered_in: wvu_knapsack (console error audit)
source: console-error-audit-session
date_discovered: 2026-08-05
assigned_to: ~
status: backlog
notes: "Not urgent. Check samvera/hyrax issues first before opening duplicate. Address after Knapsack stabilization."
---

## Summary

Browser console error occurring on every page load:

```
Uncaught Error: See almond README: incorrect module build, no module name
    at define (application-b598ce300d44299a086f47eb4851afc3c452206945d5a5350addb20f15e0394e.js:107571:19)
    at application-b598ce300d44299a086f47eb4851afc3c452206945d5a5350addb20f15e0394e.js:125560:5
    at application-b598ce300d44299a086f47eb4851afc3c452206945d5a5350addb20f15e0394e.js:125563:3
```

## Root Cause

**Location**: Hyrax gem (v5.2.0, main branch)  
**File**: [hyrax.gemspec line 41](https://github.com/samvera/hyrax/blob/main/hyrax.gemspec#L41)

```ruby
spec.add_dependency 'almond-rails', '~> 0.1'
```

The almond-rails AMD module loader (v0.3.0, declared in Hyrax's gemspec) is encountering an unnamed module definition in the compiled asset pipeline. This typically occurs when:

1. A JavaScript library in Hyrax's dependencies defines an AMD module without providing a name in the `define()` call
2. UMD (Universal Module Definition) modules that don't properly format for AMD loaders
3. Asset pipeline concatenation order placing an unnamed module where almond expects a name

## Investigation Needed

**Step 1: Check for existing issue**
- Search samvera/hyrax GitHub issues for: "almond-rails", "unnamed module", "asset pipeline"
- If issue exists, link it and document findings
- If not, create issue following workflow below

**Step 2: Identify the source gem**
- Which gem in Hyrax's dependencies is defining the unnamed module?
- Check Hyrax gems with significant JavaScript assets (iiif_manifest, openseadragon, codemirror, fabric.js, etc.)
- Look for UMD patterns that aren't compatible with strict AMD loaders

**Step 3: Almond-rails compatibility**
- Is v0.3.0 the issue or is this a known incompatibility?
- Check almond-rails gem changelog for fixes
- Check if newer version resolves the issue
- Verify almond-rails v0.3.0 satisfies `~> 0.1` constraint (it does; allows 0.1 to <0.2 OR latest in 0.x)

**Step 4: Explore workarounds**
- Asset pipeline ordering
- Almond-rails initialization parameters
- Conditional asset inclusion
- Consider upgrading to Rails 7.2+ asset pipeline alternatives

## Constraints

- Must be fixed in Hyrax gem itself (not workaround in Knapsack or Hyku)
- Cannot modify almond-rails gem locally
- Must maintain compatibility with current Rails 7.2 / Hyrax v5.x stack
- Solution should benefit all Hyrax users
- Non-urgent: Can wait until after Knapsack stabilization

## Non-Functional Impact

- Console shows error but doesn't prevent page functionality
- Error appears on every page load (low noise impact, but indicates asset pipeline issue)
- Does not affect user workflows
- **Not a blocker for Knapsack deployment**

## Workflow for Resolution

1. **Initial Research** (FIRST):
   - Check samvera/hyrax existing issues: https://github.com/samvera/hyrax/issues
   - Search terms: almond-rails, unnamed module, asset pipeline
   - Document any related issues or PRs
   - If issue already tracked, link to it and close this task

2. **Open GitHub issue** in samvera/hyrax (if not already open):
   - Repo: https://github.com/samvera/hyrax
   - Title: "almond-rails: Unnamed module error in AMD asset pipeline (v0.3.0)"
   - Include: Error message, reproduction steps, browser console screenshot
   - Link to: almond-rails gem, related Rails asset pipeline issues
   - Environment: Rails 7.2, Hyrax v5.2.0, Ruby 3.3
   - Note: This affects Hyrax and all downstream users (Hyku, Knapsack, etc.)

3. **Investigation phase** (in Hyrax repo):
   - Build asset pipeline with verbose logging
   - Identify which gem in Hyrax's dependencies contributes the unnamed module
   - Check if gem has newer version or known workaround
   - Test in Hyrax main branch

4. **Solution options** (in priority order):
   - **Option A** (preferred): Upgrade almond-rails to version that handles unnamed modules gracefully
   - **Option B**: Upgrade or replace the problematic JavaScript gem (iiif_manifest, openseadragon, etc.)
   - **Option C**: Configure Rails asset pipeline to work around the issue
   - **Option D**: Patch almond-rails locally in Hyrax if no upstream fix available

5. **PR workflow** (in samvera/hyrax):
   - Create feature branch: `fix/almond-unnamed-module-error`
   - Implement resolution
   - Test on development and staging environments
   - Document any asset pipeline changes
   - Submit PR to samvera/hyrax main branch
   - Merge to main

6. **Downstream updates**:
   - After Hyrax PR merges, test in Hyku using updated Hyrax
   - Hyku users will automatically get fix when they update to new Hyrax version
   - Knapsack will inherit fix through Hyku submodule update

## Acceptance Criteria

- [ ] Checked samvera/hyrax issues for duplicates
- [ ] GitHub issue opened in samvera/hyrax (or linked to existing issue)
- [ ] Root cause identified (which gem, why unnamed)
- [ ] Proposed solution documented in Hyrax issue
- [ ] Solution implemented and tested in Hyrax main branch
- [ ] Pull request merged to samvera/hyrax main
- [ ] Downstream testing complete (Hyku, Knapsack)
- [ ] Knapsack submodule updated to include fix
- [ ] Console error eliminated on all pages
- [ ] No new errors introduced in asset pipeline

## Governance Notes

**Why Hyrax, not Hyku?**
- almond-rails is declared in Hyrax's gemspec as a core dependency
- Issue originates in Hyrax's asset pipeline compilation
- Hyku (hyrax-webapp) simply inherits Hyrax's gems
- Knapsack cannot modify submodule (governance constraint)
- Fixing at Hyrax level benefits entire Samvera community

**Why not just a Knapsack workaround?**
- Cannot modify hyrax-webapp submodule directly
- Workarounds would duplicate code and maintainability burden
- Proper fix should go upstream where it benefits all users
- Follows Knapsack governance: "improvements to shared code go upstream"

**Issue Escalation Path**:
1. Discovered in WVU Knapsack (this session)
2. Root cause traced to Hyrax (almond-rails dependency)
3. Check existing Hyrax issues first (avoid duplicates)
4. Escalate to Hyrax community → samvera/hyrax GitHub
5. Fix implemented in Hyrax main branch
6. Hyku inherits fix (via Hyrax update in hyrax-webapp)
7. Knapsack inherits fix (via Hyku submodule update)

## Technical Details

**Issue Location**: Hyrax gem (v5.2.0, main branch)  
**Root Cause**: Hyrax's gemspec declares `almond-rails ~> 0.1` which resolves to v0.3.0

**Current Stack**:
- Hyrax: 5.2.0 (from samvera/hyrax main branch)
- almond-rails: 0.3.0 (declared in Hyrax gemspec)
- Rails: 7.2.3
- Ruby: 3.3.10

**Affected Environments**:
- Development (local Stack Car in Knapsack)
- All Hyrax instances
- All Hyku deployments (inherits Hyrax)
- All Knapsack deployments (inherits Hyku → Hyrax)

**Asset Pipeline Context**:
- Hyrax includes: jquery, bootstrap, codemirror, fabric.js, openseadragon, iiif_manifest, etc.
- Compiled asset: ~1MB minified JavaScript
- AMD loader (almond-rails) fails when unnamed module encountered

## References

- Hyrax repository: https://github.com/samvera/hyrax
- Hyrax gemspec (almond-rails dependency): https://github.com/samvera/hyrax/blob/main/hyrax.gemspec#L41
- Hyrax issues: https://github.com/samvera/hyrax/issues
- almond-rails gem: https://github.com/rails/almond-rails
- AMD/almond README: http://github.com/jrburke/almond
- Error: "See almond README: incorrect module build, no module name"
- Related: Asset pipeline in Rails 7.2

## Priority Notes

**Why deferred?**
- Non-blocking issue (functionality works, console noise only)
- Knapsack stabilization takes priority
- Better to batch upstream contributions together
- May already be tracked in Hyrax issues

**Best time to address:**
- After Knapsack critical issues resolved
- When you have community development cycles allocated
- Group with other upstream contributions to Hyrax
- Can batch multiple fixes into one PR cycle
