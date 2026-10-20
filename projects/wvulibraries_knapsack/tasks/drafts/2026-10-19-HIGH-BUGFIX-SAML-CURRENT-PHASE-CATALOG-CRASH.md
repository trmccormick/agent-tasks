---
status: backlog
resume: false
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section is complete and accurate
- [ ] All Step 0-N instructions are clear and actionable
- [x] Synthesis report template is provided
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths verified to exist (local)
- [x] Architecture Gotchas are specific
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: <agent-tasks>/projects/wvulibraries_knapsack/tasks/backlog/[FILENAME].md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvulibraries_knapsack/tasks/backlog/[FILENAME].md \
         projects/wvulibraries_knapsack/tasks/active/[FILENAME].md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/wvulibraries_knapsack/tasks -name "[FILENAME].md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/wvulibraries_knapsack/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for chat only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

---

# TASK: Monkey-Patch SAML `current_phase` to Fix Catalog Crash on Hyku 7.2
**Status**: BACKLOG
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-10-19
**Last Updated**: 2026-10-19

---

## Context
After upgrading wvu_knapsack to Hyku 7.2 on hykudev, the catalog page crashes with:
```
NoMethodError (undefined method `+' for true):
lib/omni_auth/strategies/saml_decorator.rb:32:in `other_phase'
```

The error originates from inside omniAuth's SAML strategy class — `current_phase` was changed from an integer to a boolean between Hyku 7.1 and 7.2, but somewhere in the call chain a method still does `current_phase + 1`. This blocks ALL catalog access (including facet limiter testing).

The hyrax-webapp submodule is at `00cf281e` on `7.2-stable` on both local and hykudev. Neither should be edited directly since it's tracked by the parent repo.

---

## Problem Statement
**Error**: `NoMethodError (undefined method '+' for true)`  
**Location**: Called from `lib/omni_auth/strategies/saml_decorator.rb:32` (`super` call)  
**Trigger**: GET `/catalog?search_field=all_fields&q=` on hykudev running Hyku 7.2  
**Impact**: Catalog page returns "We're sorry, but something went wrong" — prevents all feature testing

**Current behavior**: Any request to /catalog crashes with SAML auth error  
**Expected behavior**: Catalog loads normally; facets are limited (date_created=5, location_sim=5, people_represented_sim=5)

---

## Files Involved

### Primary Files — you will create/edit these
| File | Purpose | Key Section |
|------|---------|-------------|
| `config/initializers/saml_phase_patch.rb` (NEW) | Monkey-patch to fix `current_phase` type | Override method that does `+ 1` |

### Reference Files — read but do not edit
| File | Why You Need It |
|------|-----------------|
| `hyrax-webapp/lib/omni_auth/strategies/saml_decorator.rb` | Call site — line 32 calls `super` which triggers the crash |
| `/path/to/bundle/gems/*/omniauth-saml*/lib/*` (on hykudev) | Source of the bug — find where `current_phase + 1` happens |
| `hyrax-webapp/lib/omni_auth/strategies/*.rb` | Understand the full strategy chain |

---

## Architecture Gotchas

⚠️ **GOTCHA 1: Do NOT edit hyrax-webapp directly**
- ❌ Wrong: `cd hyrax-webapp && git checkout -b saml-fix && vim ...` (you'll lose changes on next `git submodule update`)
- ✅ Right: Create a Rails initializer that overrides the method at runtime via method aliasing/alias_method_chain
- Why: The parent repo controls the exact SHA. Any local edits in hyrax-webapp get lost or overwritten

⚠️ **GOTCHA 2: The bug is inside a gem, not your code**
- ❌ Wrong: Search only in `lib/`, `app/`, `config/` for the error source (it's in bundle/gems)
- ✅ Right: On hykudev, use Rails console to find which method does `current_phase + 1`:
  ```ruby
  # In rails console on hykudev:
  OmniAuth::Strategies::SAML.instance_method(:current_phase).source_location
  ```
- Why: The omniAuth-saml gem is bundled with Hyku 7.2 — the bug is in the dependency, not your application code

⚠️ **GOTCHA 3: Patch must survive container rebuild**
- ❌ Wrong: Apply a .patch file that modifies bundled gems (gets blown away on restart)
- ✅ Right: Create a Rails initializer (`config/initializers/*.rb`) — Rails loads these after all gems are loaded
- Why: Docker rebuilds the image from scratch; only `app/`, `lib/`, and `config/` survive as volume-mounted code

---

## Implementation Steps

### Step 1 — Identify the exact method doing `current_phase + 1` on hykudev

On hykudev, start a Rails console:
```bash
cd /path/to/rails   # or whatever your app directory is on hykudev
bundle exec rails console
```

Then find the culprit:
```ruby
# Check if current_phase exists on SAML strategy
s = OmniAuth::Strategies::SAML.new(nil, {})
s.respond_to?(:current_phase)
s.current_phase.class  # Should be Integer, but is Boolean — this is the bug

# Find which method does the + operation
OmniAuth::Strategies::SAML.instance_methods.grep(/phase/)
```

If you can read the source:
```ruby
require 'omniauth-saml'
puts OmniAuth::Strategies::SAML.instance_method(:current_phase).source_location
```

**Report back**: The exact class and method name that does `current_phase + 1`

### Step 2 — Create the monkey-patch initializer

Create `config/initializers/saml_phase_patch.rb`:

```ruby
# Monkey-patch to fix Hyku 7.2 SAML current_phase regression
# where current_phase returns a boolean instead of an integer,
# causing "undefined method '+' for true" when called with + 1
#
# Source: hyrax-webapp/lib/omni_auth/strategies/saml_decorator.rb:32 (call site)
# Bug: omniAuth gem in Hyku 7.2 changed current_phase return type

Rails.configuration.to_prepare do
  OmniAuth::Strategies::SAML.class_eval do
    # Override to ensure integer return value
    unless method(:current_phase).source_location&.first&.include?('saml_decorator')
      alias_method :_original_current_phase, :current_phase if respond_to?(:current_phase, true)
      
      def current_phase
        val = _original_current_phase rescue super
        # If it returned a boolean, convert to integer
        (val == true) ? 1 : ((val == false) ? 0 : val.to_i)
      end
    end
  end
end
```

**Alternative approach** — if you identify the specific method that does `+ 1`:
```ruby
Rails.configuration.to_prepare do
  OmniAuth::Strategies::SAML.class_eval do
    alias_method :_original_target_method, :target_method
    
    def target_method(*args)
      # Wrap the return to ensure it's numeric where expected
      result = _original_target_method(*args)
      result
    end
  end
end
```

### Step 3 — Deploy and verify on hykudev

1. Copy the initializer to hykudev:
   ```bash
   scp config/initializers/saml_phase_patch.rb tam0013@hykudev.lib.wvu.edu:/path/to/wvu_knapsack/config/initializers/
   ```

2. Restart Rails (touch tmp/restart.txt or `docker compose restart web`)

3. Verify the catalog loads without errors:
   ```bash
   curl -s http://localhost/catalog | grep -c "html"  # Should return > 0, not empty
   ```

---

## Acceptance Criteria

- [ ] GET `/catalog` returns HTTP 200 with valid HTML (not a crash page)
- [ ] No `NoMethodError` or `undefined method '+' for true` in production.log
- [ ] Catalog facets show correct limits: date_created=5, location_sim=5, people_represented_sim=5
- [ ] Facet "More" links appear for facets with additional results beyond the limit
- [ ] SAML login flow still works (not broken by the patch)
- [ ] The fix is in `config/initializers/saml_phase_patch.rb` — NOT in hyrax-webapp

---

## Stop Conditions

Stop and report if:
- The omniAuth gem source cannot be found on hykudev (may need to check local bundle/gems path)
- The initializer causes a new error (report the exact error message)
- SAML login breaks after the patch (the patch should only affect `current_phase`, not login flow)

---

## After the edit

Commit locally, test on hykudev, verify catalog loads. If working, create Phase 2 task for upstream PR to samvera/hyku or open an issue there.
