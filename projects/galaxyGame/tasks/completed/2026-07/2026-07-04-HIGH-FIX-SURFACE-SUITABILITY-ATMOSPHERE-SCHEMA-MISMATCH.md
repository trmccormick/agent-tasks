---
status: backlog
priority: HIGH
type: bug-fix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send the agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/backlog/current/2026-07-04-HIGH-FIX-SURFACE-SUITABILITY-ATMOSPHERE-SCHEMA-MISMATCH.md

READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.
```

---

# TASK: Fix SurfaceSuitabilityAnalyzer — Atmosphere model has no `name` attribute
**Status**: BACKLOG
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-07-04
**Phase**: phase1+ (gates Phase 2)

---

## Context

`surface_suitability_analyzer_spec.rb:152 + :157` fail because the spec tries to set `atmosphere.update!(name: "thin")` but the `CelestialBodies::Spheres::Atmosphere` model has no `name` attribute. This is a **spec schema issue** — the test fixture uses wrong attributes for the Atmosphere model.

This blocks Phase 2 work (lat/lon mapping, crater data) — must be fixed first.

---

## Problem Statement

**Error**:
```
ActiveModel::UnknownAttributeError:
  unknown attribute 'name' for CelestialBodies::Spheres::Atmosphere.
```

**Root cause**: The spec fixture uses `atmosphere.update!(name: "thin")` but the Atmosphere model's attributes are different (pressure, temperature, etc.). Need to find the correct attribute(s) to set.

---

## Files Involved

### Primary — edit these
| File | Purpose | Key Section |
|---|---|---|
| `galaxy_game/spec/services/ai_manager/surface_suitability_analyzer_spec.rb:148` | Fix atmosphere fixture to use correct attributes | lines ~148, 152, 157 |

### Reference — read only
| File | Why You Need It |
|---|---|
| `galaxy_game/app/models/celestial_bodies/spheres/atmosphere.rb` | See actual Atmosphere model attributes |
| `spec/factories/` — atmosphere factory (if exists) | See correct attribute values |

---

## Architecture Gotchas

⚠️ **GOTCHA 1: This is a spec issue, not a service code issue**
- ❌ Wrong: Add a `name` attribute to the Atmosphere model
- ✅ Right: Fix the spec to use the correct Atmosphere attributes
- Why: The Atmosphere model has `pressure`, `temperature`, `total_atmospheric_mass` — no `name`. The spec is using wrong fixture data.

⚠️ **GOTCHA 2: Check what attribute controls atmosphere type**
- The service may use `pressure` or another attribute to determine atmosphere factor
- Find the correct attribute(s) that map to "thin" atmosphere behavior
- May need to check how the analyzer reads atmosphere data

---

## Implementation Steps

### Step 1 — Find the correct Atmosphere attributes

```bash
grep -n "attr\|store_accessor\|validates\|attribute" galaxy_game/app/models/celestial_bodies/spheres/atmosphere.rb | head -20
```

Also check if there's an atmosphere factory:
```bash
grep -rn "atmosphere" spec/factories/ | grep -i "thin\|type\|name" | head -10
```

### Step 2 — Fix the spec fixture

**Before** (line ~148):
```ruby
celestial_body.atmosphere.update!(name: "thin")
```

**After** (use correct attributes based on findings from Step 1):
```ruby
# Example — actual values depend on what the model expects:
celestial_body.atmosphere.update!(pressure: 0.006, temperature: 250)
```

### Step 3 — Verify syntax

```bash
ruby -c galaxy_game/spec/services/ai_manager/surface_suitability_analyzer_spec.rb 2>&1
```

Must return: `Syntax OK`

### Step 4 — Run targeted specs

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/surface_suitability_analyzer_spec.rb:152 spec/services/ai_manager/surface_suitability_analyzer_spec.rb:157 --format progress 2>&1' | tail -5
```

Target: 0 failures.

### Step 5 — Run full SurfaceSuitabilityAnalyzer suite

```bash
docker exec web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/surface_suitability_analyzer_spec.rb --format progress 2>&1' | tail -10
```

Verify no regressions. Note: `:71` failure is a separate issue (slope calc) — it may still fail here.

### Step 6 — Commit

```bash
git add galaxy_game/spec/services/ai_manager/surface_suitability_analyzer_spec.rb
git commit -m "fix: correct atmosphere fixture in SurfaceSuitabilityAnalyzer spec to use valid attributes"
git push
```

---

## Acceptance Criteria
- [ ] `surface_suitability_analyzer_spec.rb:152 + :157` pass (0 failures)
- [ ] No regressions in other SurfaceSuitabilityAnalyzer specs (except :71 which is separate)
- [ ] Spec uses correct Atmosphere model attributes
- [ ] Spec fix committed

---

## Stop Conditions — escalate to user immediately if:
- Cannot determine which attribute controls atmosphere type — stop and report findings
- Fix causes new failures in specs you did not touch
- The analyzer service reads atmosphere data in an unexpected way — stop and report

---

## Commit Instructions
```bash
git add galaxy_game/spec/services/ai_manager/surface_suitability_analyzer_spec.rb
git commit -m "fix: correct atmosphere fixture in SurfaceSuitabilityAnalyzer spec to use valid attributes"
git push
```

---

## Completion Report
**Completed by**: [agent]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures (excluding :71 which is separate)

### What was changed
- `[file]` — [description]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

---

## Handoff Summary
HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
