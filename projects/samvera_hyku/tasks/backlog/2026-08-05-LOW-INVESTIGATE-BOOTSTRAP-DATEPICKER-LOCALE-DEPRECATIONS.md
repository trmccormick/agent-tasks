---
id: bootstrap-datepicker-locale-2026-08-05
project: samvera_hyku
title: "Investigate and fix bootstrap-datepicker-rails deprecated locale warnings"
severity: LOW
priority: deferred
category: upstream-gem-issue
related_issues: []
discovered_in: wvu_knapsack (console error audit)
source: console-error-audit-session
date_discovered: 2026-08-05
assigned_to: ~
status: backlog
notes: "Non-blocking warnings only. Check if hyrax-webapp gem version can be updated. If not, may need Knapsack workaround to exclude deprecated locales."
---

## Summary

Browser console warnings on every page load:

```
DEPRECATED: This filename doesn't follow the convention, use bootstrap-datepicker.en-CA.js instead.
$.fn.datepicker.deprecated @ application-...js

DEPRECATED: The language code "kh" is deprecated and will be removed in 2.0. For Khmer support use "km" instead.
DEPRECATED: The language code "kr" is deprecated and will be removed in 2.0. For korean support use "ko" instead.
DEPRECATED: This language code "rs-latin" is deprecated (invalid serbian language code) and will be removed in 2.0. For Serbian latin support use "sr-latin" instead.
DEPRECATED: This language code "rs" is deprecated (invalid serbian language code) and will be removed in 2.0. For Serbian support use "sr" instead.
```

## Root Cause

**Location**: hyrax-webapp submodule  
**File**: [hyrax-webapp/Gemfile](https://github.com/samvera/hyrax-webapp/blob/main/Gemfile) declares `gem 'bootstrap-datepicker-rails'`

**Version**: bootstrap-datepicker-rails v1.10.0.1 (latest available)

**Problem**: The gem bundles bootstrap-datepicker JavaScript library with deprecated locale files:
- Old filename convention: `en-CA.js` (should be `bootstrap-datepicker.en-CA.js`)
- Deprecated language codes in asset pipeline: `kh`, `kr`, `rs-latin`, `rs` 
- These codes should be: `km`, `ko`, `sr-latin`, `sr`

**Why no fix available**: 
- Latest gem version is 1.10.0.1 (checked 2026-08-05)
- Gem maintainer (Nerian/bootstrap-datepicker-rails) has not updated locale files
- Bootstrap-datepicker library itself evolved but gem wasn't updated

## Investigation Needed

**Step 1: Assess options**
1. **Option A** (preferred): Can hyrax-webapp update to newer gem version?
   - Check if newer version exists upstream (npm, rubygems, GitHub)
   - Check if gem repo still maintained
   - Try `bundle update bootstrap-datepicker-rails` in hyrax-webapp

2. **Option B**: Workaround in hyrax-webapp Gemfile
   - Pin specific version, update gemspec if needed
   - Document why in gemfile comments

3. **Option C**: Knapsack workaround
   - Exclude deprecated locale files from asset pipeline via Rails config
   - Only viable if Option A/B not possible
   - Less ideal (adds complexity to Knapsack for upstream issue)

**Step 2: Asset pipeline investigation** (in context of Knapsack)
- Where are locale files loaded in asset pipeline?
- Can they be excluded via Rails initializer?
- Example approach:
  ```ruby
  # config/initializers/bootstrap_datepicker.rb
  # Exclude deprecated locale files from bootstrap-datepicker-rails gem
  # to prevent console warnings about deprecated locale codes
  ```

**Step 3: Check related issues**
- GitHub issues in Nerian/bootstrap-datepicker-rails repo
- Any related issues in Hyrax or Hyku
- Community discussions about this gem's maintenance status

## Constraints

- Cannot modify hyrax-webapp submodule directly (governance constraint)
- Bootstrap-datepicker-rails is declared in hyrax-webapp Gemfile (not Knapsack)
- If workaround needed, should be minimal and document upstream issue clearly

## Non-Functional Impact

- Console shows deprecation warnings but doesn't prevent functionality
- Warnings appear on every page load (noise in dev console)
- Does not affect user workflows
- **Not a blocker for Knapsack deployment**

## Workflow for Resolution

1. **Initial Assessment** (FIRST):
   - Check bootstrap-datepicker-rails GitHub repo for current status
   - Search for open issues about locale deprecations
   - Determine if gem is still maintained or abandoned
   - Document findings

2. **Option A: Gem upgrade**
   - If newer version exists: Try updating in hyrax-webapp
   - Test in hyrax-webapp with new version
   - If successful: File PR in samvera/hyrax-webapp
   - If not available: Move to Option B/C

3. **Option B: hyrax-webapp gemfile fix**
   - If gem maintainer has released fix (different gem name, fork, replacement)
   - Document in Gemfile why switching gems
   - File PR in samvera/hyrax-webapp

4. **Option C: Knapsack workaround** (fallback)
   - Create Rails initializer that excludes deprecated locales from asset pipeline
   - Document clearly that this is workaround for upstream gem issue
   - Add comment linking to relevant GitHub issues

5. **Communication**
   - If gem is abandoned: Consider contributing to gem or finding replacement
   - Document decision in hyrax-webapp for other users

## Acceptance Criteria

- [ ] Checked bootstrap-datepicker-rails repo for current maintenance status
- [ ] Determined which resolution path is viable (A, B, or C)
- [ ] If Option A/B: PR submitted to hyrax-webapp
- [ ] If Option C: Knapsack workaround implemented with clear documentation
- [ ] Console warnings eliminated or documented as known upstream issue
- [ ] No new errors introduced in asset pipeline

## Governance Notes

**Why not just Knapsack workaround?**
- This is a shared gem dependency used by all Hyku/Hyrax instances
- Fix should benefit entire community, not just Knapsack
- If workaround needed, should be temporary pending upstream fix
- Upstream fix is preferred to avoid code duplication

**If workaround needed:**
- Create Rails initializer: `config/initializers/bootstrap_datepicker_locale_workaround.rb`
- Document clearly: "Temporary workaround for upstream bootstrap-datepicker-rails gem issue"
- Link to: hyrax-webapp Gemfile issue and bootstrap-datepicker-rails repo status
- Add TODO to remove when upstream fixed

## Technical Details

**Gem Version**: bootstrap-datepicker-rails 1.10.0.1  
**Root Cause**: Bundled bootstrap-datepicker JS library uses deprecated locale codes

**Affected Locales**:
- `kh` → deprecated (use `km` for Khmer)
- `kr` → deprecated (use `ko` for Korean) 
- `rs-latin` → deprecated (use `sr-latin` for Serbian Latin)
- `rs` → deprecated (use `sr` for Serbian)

**Current Stack**:
- Hyrax: 5.2.0
- hyrax-webapp: main branch
- Rails: 7.2.3
- Ruby: 3.3.10

**Asset Pipeline**:
- Compiled asset loads all locale files from bootstrap-datepicker-rails gem
- No current mechanism to exclude specific locales
- Would require Rails initializer config or gem fork

## References

- bootstrap-datepicker-rails GitHub: https://github.com/Nerian/bootstrap-datepicker-rails
- Bootstrap-datepicker (JS library): https://github.com/uxsolutions/bootstrap-datepicker
- hyrax-webapp Gemfile: https://github.com/samvera/hyrax-webapp/blob/main/Gemfile
- Issue origin: WVU Knapsack console audit (2026-08-05)

## Priority Notes

**Why deferred?**
- Console warnings only, no functional impact
- Requires upstream investigation (gem status, maintenance)
- Better to batch with other upstream work
- Not blocking Knapsack deployment

**Best time to address:**
- After Knapsack stabilization
- With community development cycles
- Possibly with almond-rails issue as part of gem audit
- Consider creating Hyrax/Hyku community issue to track

## Related Issues

- `2026-08-05-LOW-INVESTIGATE-ALMOND-UNNAMED-MODULE-ERROR.md` (similar upstream gem issue in Hyrax)
- Both are non-blocking gem dependency issues
- Both could be batched into upstream contribution cycle
