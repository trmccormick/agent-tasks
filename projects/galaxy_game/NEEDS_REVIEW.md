# Needs Review

Short-list of items flagged for Claude (planning review) or human decision.
Qwen: add an entry here instead of just noting something in status.md when
an escalation trigger below fires. Remove/archive entries once resolved —
this file should stay small. Full history stays in status.md.

---

## Escalation triggers — add an entry here when:

- Any file operation touches data/json-data/ or another gitignored path
  (wrong location, git add -f, revert of a move+tracking commit — this
  class of bug has hit 3 times, always needs a second check)
- A task is marked "complete" but the completion claim was not independently
  re-verified in the SAME session (e.g. you fixed something and inferred it
  works, but didn't re-run the actual test/rake/grep after the fix)
- A task's fix touches a system another already-completed task built
  assumptions on top of (cross-task dependency risk)
- Two research/design documents disagree about the same system
- You catch yourself repeating an identical action/output 3+ times without
  progress — stop, write an entry here, don't keep retrying

---

## Entry template

### [DATE] — [task name or file]
**What happened**: 
**What I already checked**: 
**What needs a second opinion**: 
**Status**: OPEN / RESOLVED (date + how)

---

## Current entries

### 2026-07-21 — InfrastructureCostCalculator calls non-existent method
**What happened**: infrastructure_cost_calculator.rb:152 calls `AIManager::PrecursorCapabilityService.can_produce?(destination, material.chemical_formula)` — a two-arg method that does not exist on PrecursorCapabilityService. Only `can_produce_locally?(resource)` (one-arg) exists.

**What I already checked**:
- Confirmed via grep: only `can_produce_locally?(resource)` exists in precursor_capability_service.rb
- Confirmed the one call site at infrastructure_cost_calculator.rb:152
- Confirmed `can_produce_locally?` resolves celestial_body internally via settlement.location.celestial_body — doesn't accept a destination param even if renamed
- No specs exist for InfrastructureCostCalculator (grep spec/ returned zero matches)
- One caller in test script only: galaxy_game/test_realistic_costs.rb (not in spec/)

**What needs a second opinion**:
- Is this code path ever actually exercised? (calculate_local_production_discount is called from calculate_cost, need to confirm whether calculate_cost has any live callers/specs, or if this is dead/untested code)
- If live: fix is to change the call to `can_produce_locally?(material.chemical_formula)` and remove the destination arg — but confirm InfrastructureCostCalculator has access to the right settlement context for that method to resolve celestial_body correctly, since it currently operates on a destination CelestialBody directly, not a settlement

**Status**: OPEN
