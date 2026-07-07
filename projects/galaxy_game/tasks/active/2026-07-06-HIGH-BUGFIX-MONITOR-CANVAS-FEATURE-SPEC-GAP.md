---
status: backlog
priority: HIGH
type: bugfix
system_domain: TESTING_RSPEC
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Bug Fix Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-06-HIGH-BUGFIX-MONITOR-CANVAS-FEATURE-SPEC-GAP.md

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv to new folder
  - New/untracked file: move with filesystem (mv), then git add the final path
  - Never copy task files between folders
READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# BUGFIX: Create Missing Feature Spec for Admin Monitor Canvas

**Status**: BACKLOG
**Priority**: HIGH
**Type**: bugfix
**Created**: 2026-07-06 — Planning Agent (Qwen3.6-35b)

---

## Context

The admin monitor canvas (`app/javascript/admin/monitor.js`) has no corresponding feature spec. This is a testing gap that needs closing to ensure canvas rendering behavior is covered by RSpec.

**Current state**: No `spec/features/admin/planetary_monitor_spec.rb` exists
**Expected state**: Feature spec covering canvas rendering on page load, data injection, and error handling

**Phase alignment**: Testing gap — placed in `backlog/current/` to close the missing RSpec coverage. This is urgent because without specs, regressions in canvas rendering won't be caught.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (BUGFIX Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1**: Feature specs require JS driver (Capybara + Selenium/DryRunner)
- ❌ Wrong: Write unit-style tests in feature spec location
- ✅ Right: Use `driven_by :rack_test` for non-JS assertions, `driven_by :selenium` or similar for canvas state checks
- Why: Canvas rendering is JS-driven; need appropriate driver

⚠️ **GOTCHA 2**: Monitor data is injected via `<script type="application/json">` element
- ❌ Wrong: Try to mock JS variables directly in RSpec
- ✅ Right: Verify the ERB view renders correct JSON data in `#monitor-data` element
- Why: The view passes data to JS via DOM, not via controller instance variables

⚠️ **GOTCHA 3**: Multiple views share the same monitor.js
- ❌ Wrong: Only test `planetary.html.erb`
- ✅ Right: Include tests for at least `regional.html.erb` as well
- Why: Same script serves multiple admin views; regression in one could break others

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

```
## STATUS SYNTHESIS REPORT

**Task**: Create Missing Feature Spec for Admin Monitor Canvas
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Create spec/features/admin/planetary_monitor_spec.rb covering canvas rendering,
data injection, and error handling. Verify existing factories support celestial_body
with geosphere/atmosphere/hydrosphere associations needed for monitor views.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `spec/features/admin/planetary_monitor_spec.rb` | New feature spec — to create | not started |
| `app/views/admin/celestial_bodies/planetary.html.erb` | View under test | pending |
| `spec/factories/celestial_body.rb` | Factory for test data | pending |
| `spec/factories/geosphere.rb` | Geosphere association factory | pending |

### Prerequisites Completed
- ✅ Read README.md BUGFIX section
- ✅ Read project guide
- ✅ Understand architecture gotchas above

### Expected Outcomes
- Feature spec created with canvas rendering tests
- Data injection validation tests
- Error handling tests for missing terrain data
- All specs passing in isolation

### Critical Gotchas I Will Avoid
- ❌ Writing unit tests in feature spec — instead ✅ Use appropriate Capybara driver
- ❌ Only testing planetary view — instead ✅ Include regional view tests
- ❌ Assuming factories exist — instead ✅ Verify/create needed factories first

---

**SYNTHESIS COMPLETE.** Ready to proceed with spec creation.
```

---

## Problem Statement

### Current State — What Exists
- ✅ `app/javascript/admin/monitor.js` — 1364+ lines, full canvas rendering module
- ✅ `app/views/admin/celestial_bodies/planetary.html.erb` — admin planetary view with canvas
- ❌ **Missing**: `spec/features/admin/planetary_monitor_spec.rb` — no feature spec for canvas rendering

### Test Scenarios to Cover

**Scenario 1 — Canvas renders on page load**
```ruby
# Verify canvas element is present and monitor-data JSON is injected correctly
```

**Scenario 2 — Data injection with terrain map**
```ruby
# Verify #monitor-data contains correct planet_data, terrain_data, available_layers
```

**Scenario 3 — Missing terrain data handling**
```ruby
# Verify graceful degradation when geosphere has no terrain_map
```

**Scenario 4 — Multiple view coverage**
```ruby
# Verify regional.html.erb also renders canvas correctly
```

---

## Implementation Steps

### Step 1 — Audit Existing Factories (Research)
**Goal**: Verify factories support celestial_body with required associations.

```bash
# Check existing factories:
grep -n "geosphere\|atmosphere\|hydrosphere" galaxy_game/spec/factories/celestial_body.rb

# Check if geosphere factory has terrain_map attribute:
cat galaxy_game/spec/factories/geosphere.rb 2>/dev/null | head -30
```

**Deliverable**: Factory audit report — what exists, what needs creating.

### Step 2 — Create Feature Spec (Implementation)
**Goal**: Create comprehensive feature spec for admin monitor canvas.

```ruby
# spec/features/admin/planetary_monitor_spec.rb

require 'rails_helper'

RSpec.describe 'Admin Planetary Monitor', type: :feature do
  let(:celestial_body) { create(:celestial_body, :with_geosphere, name: 'Luna') }

  describe 'canvas rendering on page load' do
    it 'renders canvas element with monitor data' do
      visit admin_celestial_body_planetary_path(celestial_body)
      
      expect(page).to have_css('#planetCanvas')
      expect(page).to have_css('#monitor-data')
    end
    
    it 'injects correct planet data into monitor-data element' do
      visit admin_celestial_body_planetary_path(celestial_body)
      
      monitor_json = page.find('#monitor-data').text
      data = JSON.parse(monitor_json)
      
      expect(data['planet_name']).to eq('Luna')
      expect(data['available_layers']['terrain']).to be true
    end
    
    it 'handles missing terrain data gracefully' do
      body_without_terrain = create(:celestial_body, name: 'EmptyWorld')
      visit admin_celestial_body_planetary_path(body_without_terrain)
      
      expect(page).not_to have_content('Error')
      # Should still render canvas, just without terrain layer
    end
  end
  
  describe 'regional view' do
    it 'renders canvas for regional monitor' do
      visit admin_celestial_body_regional_path(celestial_body)
      
      expect(page).to have_css('#planetCanvas')
    end
  end
end
```

**Deliverable**: Feature spec with passing tests.

### Step 3 — Run Specs (Validation)
**Goal**: Verify all new specs pass in isolation.

```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec spec/features/admin/planetary_monitor_spec.rb
```

**Deliverable**: Green spec run with 0 failures.

---

## Acceptance Criteria
- [ ] `spec/features/admin/planetary_monitor_spec.rb` created
- [ ] Canvas rendering test passes (canvas element present on page load)
- [ ] Data injection test passes (monitor-data JSON contains correct planet info)
- [ ] Missing terrain data test passes (graceful degradation)
- [ ] Regional view test passes (same script works for regional monitor)
- [ ] Isolation run: 0 failures
- [ ] No regressions in related specs

---

## Stop Conditions — escalate to user immediately if:
- Required factories don't exist and can't be created without model changes
- Capybara driver configuration blocks JS-driven tests
- Any architectural decision is required before spec can proceed

---

## Dependencies

**Blocked by**: None — this task is ready to dispatch immediately
**Blocks**: None directly, but closes testing gap for admin monitor UI
**Related tasks**: `ui/2026-07-06-MEDIUM-BUGFIX-ADMIN-MONITOR-TURBO-CANVAS-INIT.md` (canvas rendering fix)

---

## Completion Report
*Filled in by the implementing agent after completion*
