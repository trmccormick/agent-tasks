---
id: "2026-06-15-MEDIUM-REFACTOR-BASE-CRAFT-BACKUP-METHOD-MERGE"
status: backlog
priority: MEDIUM
type: REFACTOR
created_date: 2026-06-15
last_updated: 2026-06-15
system_domain: CRAFT_MODELING
mvp_alignment: PHASE_4_CLEANUP

---

# Task: Review and Merge Valuable Methods from base_craft.rb Backup Files

## Context & Strategic Purpose

During April 2026 backlog triage (File #9), a STOP condition was triggered when auditing stale backup files alongside `base_craft.rb`. All three backup files contain **unique methods NOT present in the current version**, suggesting alternative implementations that may have been discarded during refactoring between February-May 2026.

This task documents the discovered methods and requires human decision on whether to:
1. Merge valuable logic back into base_craft.rb before deletion
2. Confirm all backup content is obsolete and safe to discard
3. Preserve specific implementations for future reference

**Why This Matters**: The backup files are from February 20, 2026 (before current version dated May 27, 2026). They may contain:
- Alternative design patterns that were abandoned mid-refactor  
- Methods removed during Housing concern extraction or other architectural changes
- Logic needed for future features like depot operations or physical property calculations

---

## Problem Statement

**Current State**: Three backup files exist with unique content blocking automated cleanup task (`2026-03-30-LOW-CHORE-REMOVE-STALE-BASE-CRAFT-FILES.md`):
```bash
$ ls -la ~/Documents/git/galaxyGame/galaxy_game/app/models/craft/base_craft.rb*  
-rw-r--r--@ 1 tam0013 staff 22592 May 27 20:39 base_craft.rb ✅ (current)
-rw-r--r--@ 1 tam0013 staff 24529 Feb 20 17:53 base_craft.rb.new ⚠️ STALE BACKUP
-rw-r--r--@ 1 tam0013 staff 18751 Feb 20 18:26 base_craft.rb.new2 ⚠️ STALE BACKUP  
-rw-r--r--  1 tam0013 staff 21175 Feb 20 21:15 base_craft.rb.new3 ⚠️ STALE BACKUP
```

**Expected State**: Human review completed, decision made on merge vs. discard, backup files safely removed or preserved as reference material.

---

## Current State Analysis

| Backup File | Date | Unique Methods/Features Found | Potential Value |
|-------------|------|-------------------------------|-----------------|
| `base_craft.rb.new` | Feb 20, 17:53 | crew_count(), mass() with inventory calc, can_process_volatiles?, enhanced has_available_docking_port? | ⚠️ **HIGH** — depot operations support, docking logic improvements |
| `base_craft.rb.new2` | Feb 20, 18:26 | Housing concern include pattern, alternative has_available_docking_port? implementation, DEFAULT_VOLUME_PER_CREW_M3 removal note | 🟡 MEDIUM — shows pre-Housing-extraction state |
| `base_craft.rb.new3` | Feb 20, 21:15 | physical_properties() method (BlueprintLookupService), mass alias to total_mass, status attribute definition | ⚠️ **HIGH** — physical dimensions lookup may be needed for future features |

---

## Implementation Scope

### In Scope
- [ ] Review each unique method against current base_craft.rb implementation  
- [ ] Determine if methods are: (a) still needed but removed by mistake, (b) superseded by better implementations elsewhere, or (c) obsolete design patterns
- [ ] If valuable → merge into base_craft.rb with appropriate testing  
- [ ] If obsolete → document rationale and proceed with backup file deletion
- [ ] Update legacy task `2026-03-30-LOW-CHORE-REMOVE-STALE-BASE-CRAFT-FILES.md` status to completed or superseded

### Out of Scope
- Refactoring other parts of base_craft.rb not related to these four methods  
- Creating new features beyond what exists in backup files  
- Modifying associated specs unless merge requires test updates

---

## Technical Requirements

### Method 1: crew_count() (from .new file)
```ruby
# Returns the crew count for the craft (from operational_data or 0)
public def crew_count
  operational_data['crew_count'] || 0
end
```

**Questions to Answer**:
- Is this method currently needed anywhere in codebase?  
- Does current base_craft.rb have alternative implementation elsewhere?
- Should this be added back for consistency with other craft methods?

### Method 2: mass() (from .new file)
```ruby
# Returns the total mass of the craft (dry mass + inventory mass)
public def mass
  dry_mass = if respond_to?(:blueprint_data) && blueprint_data&.dig('operational_requirements', 'dry_mass_kg')
    blueprint_data['operational_requirements']['dry_mass_kg'].to_f
  else
    500.0 # Default for tests and missing blueprint
  end
  
  inventory_mass = if inventory&.items
    inventory.items.sum { |item| (item.try(:mass) || 0) * item.amount }
  else
    0.0
  end
  
  dry_mass + inventory_mass
end
```

**Questions to Answer**:
- Is this calculation currently done elsewhere or not needed?  
- Does current codebase use different mass calculation approach?
- Should this be added for physics simulations, docking calculations, etc.?

### Method 3: can_process_volatiles? (from .new file)
```ruby
# Returns true if craft can process volatiles (for depots, etc.)
def can_process_volatiles?
  !!operational_data['can_process_volatiles']
end
```

**Questions to Answer**:
- Is this predicate needed for L1 Depot / gas processing pipeline features?  
- Does current codebase check volatile processing capability differently?
- Should this be added as Phase 5+ prerequisite for depot operations?

### Method 4: physical_properties() (from .new3 file)
```ruby
# Returns the physical properties (length, width, height) from the blueprint
def physical_properties
  blueprint = Lookup::BlueprintLookupService.new.find_blueprint("#{craft_name} Blueprint")
  props = blueprint&.dig('physical_properties')
  return { length_m: nil, width_m: nil, height_m: nil } unless props
  
  {
    length_m: props['length_m'],
    width_m: props['width_m'],
    height_m: props['height_m']
  }
end
```

**Questions to Answer**:
- Is physical dimensions lookup needed for docking calculations or collision detection?  
- Does BlueprintLookupService currently support this query pattern?
- Should this be added as Phase 5+ prerequisite for orbital structure deployment features?

---

## Testing Requirements

If any methods are merged:
1. **Unit tests** — Add RSpec examples covering edge cases (nil operational_data, missing blueprint data)  
2. **Integration tests** — Verify craft operations using new methods work correctly with existing systems  
3. **Regression check** — Ensure no breaking changes to current base_craft.rb functionality

---

## Dependencies & Blockers

**Blocked by**: None — this is a cleanup/audit task that can proceed independently  
**Blocks**: `2026-03-30-LOW-CHORE-REMOVE-STALE-BASE-CRAFT-FILES.md` (original backup removal task)  

### Related Tasks
- Phase 5+ tasks involving depot operations may benefit from `can_process_volatiles?` method  
- Orbital structure deployment features may need `physical_properties()` for docking calculations

---

## Success Criteria & Acceptance Tests

- [ ] All four unique methods reviewed and decision documented (merge vs. discard)
- [ ] If merged: Methods added to base_craft.rb with appropriate tests passing  
- [ ] If discarded: Rationale documented explaining why each method is obsolete/superseded
- [ ] Backup files either deleted or preserved as reference material based on decision
- [ ] Legacy task `2026-03-30-LOW-CHORE-REMOVE-STALE-BASE-CRAFT-FILES.md` updated to completed status

---

## Decision Matrix (Fill During Implementation)

| Method | Keep & Merge? | Rationale | Action Required |
|--------|---------------|-----------|-----------------|
| crew_count() | ☐ YES / ☐ NO | [Document why] | [Merge or discard] |
| mass() | ☐ YES / ☐ NO | [Document why] | [Merge or discard] |
| can_process_volatiles? | ☐ YES / ☐ NO | [Document why] | [Merge or discard] |
| physical_properties() | ☐ YES / ☐ NO | [Document why] | [Merge or discard] |

---

## Notes for Implementing Agent

**CRITICAL**: This task requires human judgment on code value. Do NOT automatically merge methods without understanding:
1. Why they were removed from current version (check git history between Feb 20 and May 27, 2026)  
2. Whether alternative implementations exist elsewhere in codebase  
3. If features are still needed for Phase 5+ or future work

**Recommended Approach**:
```bash
# Step 1: Review git history to understand why methods were removed
git log --oneline --all -- galaxy_game/app/models/craft/base_craft.rb | head -20  

# Step 2: Search codebase for alternative implementations  
grep -rn "def crew_count\|\.crew_count" ~/Documents/git/galaxyGame/galaxy_game/ --include="*.rb"
grep -rn "\.mass\|total_mass" ~/Documents/git/galaxyGame/galaxy_game/app/models/craft/ --include="*.rb"

# Step 3: Check if methods are called anywhere (indicates they're needed)  
git log -p --all -- galaxy_game/app/models/craft/base_craft.rb | grep -A5 "def crew_count\|def mass\|can_process_volatiles\|physical_properties"
```

**Escalation Required**: If uncertain about method value, escalate to strategist agent or user for decision before proceeding with merge.

---

## Completion Report Template

*Fill in after implementation:*

**Completed by**:  
**Completion date**:  
**Decision Summary**: [Brief summary of what was merged vs discarded]

### Methods Merged
- crew_count(): ☐ YES / ☐ NO — Reason: _______
- mass(): ☐ YES / ☐ NO — Reason: _______
- can_process_volatiles?: ☐ YES / ☐ NO — Reason: _______  
- physical_properties(): ☐ YES / ☐ NO — Reason: _______

### Files Changed
```bash
# List files modified during implementation
galaxy_game/app/models/craft/base_craft.rb (if merged)
galaxy_game/spec/models/craft/base_craft_spec.rb (tests added/updated)
docs/agent/archive/backlog_april_2026/deprecated/2026-03-30*.md (status updated)
```

### Backup Files Status
☐ Deleted after review completed  
☐ Preserved as reference material — Location: _______

---

**END OF TASK FILE**
