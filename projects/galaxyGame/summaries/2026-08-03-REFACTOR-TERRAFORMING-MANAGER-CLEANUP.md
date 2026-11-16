# Synthesis Report: TerraformingManager Cleanup

**Date**: 2026-08-03
**Task**: 2026-07-24-MEDIUM-REFACTOR-TERRAFORMING-MANAGER-CLEANUP.md
**File**: `galaxy_game/app/services/ai_manager/terraforming_manager.rb`

---

## Audit Results: Method Inventory

### Total Methods Found: 21

#### Pattern-Driven Logic (Primary Flow) — 7 methods
| Line | Method | Purpose |
|------|--------|---------|
| ~18 | `determine_terraforming_phase` | Determine current terraforming phase for a world |
| ~43 | `calculate_gas_needs` | Calculate atmospheric gas needs with shield-first constraint |
| ~120 | `should_seed_biosphere?` | Check if conditions are met for biosphere seeding |
| ~135 | `seed_biosphere` | Seed a new biosphere on a world |
| ~149 | `manage_oxygen_levels` | Calculate excess O2 removal plan |
| ~167 | `execute_o2_management` | Execute O2 removal via H2 reaction |
| ~190 | `calculate_methane_needs` | Calculate CH4 synthesis requirements |

#### Fallback Logic (Emergency/Edge Cases) — 5 methods
| Line | Method | Purpose |
|------|--------|---------|
| ~83 | `calculate_warming_phase_needs` | Fallback CO2 needs for warming phase |
| ~89 | `calculate_maintenance_phase_needs` | Fallback minimal maintenance needs |
| ~157 | `create_starter_organisms` | Fallback simple starter organisms (no pattern) |
| ~345 | `calculate_warming_phase_needs` (private, duplicate name!) | Detailed warming phase with pressure cutoff logic |
| ~368 | `calculate_maintenance_phase_needs` (private, duplicate name!) | Detailed maintenance phase with gas percentage tuning |

#### Orchestration (Coordination) — 5 methods
| Line | Method | Purpose |
|------|--------|---------|
| ~178 | `synthesize_methane` | Execute Sabatier reaction across worlds |
| ~220 | `plan_h2_imports` | Aggregate H2 needs for O2 management + methane |
| ~235 | `import_h2_from_gas_giant` | Import H2 from source world to destination depot |
| ~254 | `determine_phase_from_pattern` | Pattern-based phase determination |
| ~270 | `identify_available_resources` | Identify available atmospheric resources across worlds |

#### Pattern-Based Helpers — 3 methods
| Line | Method | Purpose |
|------|--------|---------|
| ~368 | `calculate_gas_needs_from_pattern` | Calculate gas needs using learned patterns |
| ~401 | `seed_biosphere_from_pattern` | Seed biosphere using learned patterns |
| ~420 | `create_optimized_starter_organisms` | Create optimized organisms from pattern data |

#### Private Helpers — 6 methods
| Line | Method | Purpose |
|------|--------|---------|
| ~458 | `default_params` (FIRST) | Comprehensive default simulation params |
| ~470 | `initialize_depots` | Create orbital depots for each world |
| ~476 | `calculate_excess_o2` | Calculate excess O2 mass for removal |
| ~483 | `get_gas_percentage` | Get percentage of a gas in atmosphere |
| ~596 | `default_params` (SECOND — **DUPLICATE BUG**) | Returns only `mars_liquid_water_threshold` |
| ~603 | `has_magnetosphere_protection?` | Check magnetosphere protection status |
| ~607 | `atmospheric_reduction_exceeded?` | Check if 5% atmospheric mass reduction cap exceeded |

---

## Duplication Analysis

### CRITICAL BUG: Duplicate `default_params` Methods (Lines ~458 and ~596)

**This is NOT intentional duplication — it's a Ruby bug.**

In Ruby, when two methods with the same name exist in a class, the **second definition overrides the first**. This means:

```ruby
# Line ~458 — DEFINED FIRST (intended comprehensive defaults)
def default_params
  {
    safe_o2_threshold: 22.0,
    target_ch4_pct: 1.0,
    target_n2_pct: 70.0,
    target_o2_pct: 18.0,
    target_co2_pct: 0.04,
    target_total_pressure_bar: 0.81,
    mars_liquid_water_threshold: 1.0,
    cycler_capacity: 1.0e13,
    titan_capacity: 1.0e13
  }
end

# Line ~596 — DEFINED SECOND (overrides the first!)
def default_params
  {
    mars_liquid_water_threshold: 1.0
  }
end
```

**Impact**: All callers of `default_params` get ONLY `mars_liquid_water_threshold: 1.0`, losing all other defaults. This is likely causing silent failures in O2 management, methane synthesis, and pressure calculations.

### True Duplication: Warming/Maintenance Phase Calculations

| Public Fallback | Private Detailed | Relationship |
|-----------------|------------------|--------------|
| `calculate_warming_phase_needs` (line ~83) | `calculate_warming_phase_needs` (private, line ~476) | **Same name!** The private one overrides the public one when called internally. Public fallback returns `{co2: 1000}`; private detailed version has pressure cutoff logic. |
| `calculate_maintenance_phase_needs` (line ~89) | `calculate_maintenance_phase_needs` (private, line ~368) | **Same name!** Same override issue. Public returns `{}`; private has full gas percentage tuning. |

**This is a naming collision bug**, not intentional delegation. The private methods shadow the public ones when called from within the class.

### Logic Duplication: Starter Organism Creation

| Method | Population | Complexity |
|--------|-----------|------------|
| `create_starter_organisms` (line ~603) | Fixed: 1B, 500M, 800M | Single species per call |
| `create_optimized_starter_organisms` (line ~420) | Proportional from total | Multiple species with distribution |

The public `seed_biosphere` method calls `create_starter_organisms` as fallback, while `seed_biosphere_from_pattern` calls `create_optimized_starter_organisms`. The logic is similar but the populations/rates differ significantly. This is **intentional** — pattern-based seeding uses optimized values from learned patterns.

---

## Section Organization Issues

The file has NO section headers. Methods are organized roughly by creation order, not by concern:
- Public methods scattered with private helpers interspersed
- `private` keyword appears TWICE (line ~456 and line ~594), which is valid Ruby but confusing
- Pattern-driven logic mixed with fallback logic throughout

---

## Stop Conditions Assessment

| Stop Condition | Status |
|---------------|--------|
| Cannot determine which "duplicated" method is canonical | **RESOLVED**: `default_params` second definition is a bug — merge them. Warming/maintenance: private detailed versions are canonical, public ones should delegate. |
| Fallback methods depend on state canonical doesn't expose | **NO ISSUE**: All methods use `@worlds`, `@simulation_params`, or world state directly |
| Removal causes failures outside terraforming/biosphere | **RISK**: Need to check callers in AI Manager services before removing any public method |
| Migration needed | **NOT REQUIRED**: Pure refactor, no schema changes |

---

## Proposed Refactoring Plan

### 1. Fix `default_params` Bug (HIGH PRIORITY)
Merge the two definitions into one comprehensive method. This is a bug fix, not just cleanup.

### 2. Fix Warming/Maintenance Naming Collision
Rename private methods to avoid shadowing:
- `calculate_warming_phase_needs` (private) → `calculate_warming_phase_needs_detailed`
- `calculate_maintenance_phase_needs` (private) → `calculate_maintenance_phase_needs_detailed`
Have public fallback methods delegate to the detailed versions.

### 3. Add Section Headers
Organize into three clear sections:
```ruby
# ========================================================================
# PUBLIC API — Decision Making
# ========================================================================

# ========================================================================
# PATTERN-DRIVEN LOGIC (Primary Flow)
# ========================================================================

# ========================================================================
# FALLBACK LOGIC (Emergency/Edge Cases)
# ========================================================================

# ========================================================================
# ORCHESTRATION (Coordination with other subsystems)
# ========================================================================

# ========================================================================
# PRIVATE HELPERS
# ========================================================================
```

### 4. Add Method Documentation
Add one-line purpose + type annotation to each public method.

### 5. Consolidate `private` Keyword
Remove the redundant `private` keyword at line ~594. Keep only one at the start of private section.

---

## Files to Check for Callers (Read-Only)

| File | Why |
|------|-----|
| `app/services/ai_manager/*` | Services that call TerraformingManager methods |
| `spec/services/ai_manager/terraforming_manager_spec.rb` | Tests revealing expected behavior |
| `spec/integration/terraforming*` | Integration tests |

---

## Risks

1. **Renaming private methods** — Safe, only called internally
2. **Merging `default_params`** — Bug fix, should not break anything (restores intended behavior)
3. **Removing public fallback methods** — RISKY if external callers depend on them. Must verify before removal.
4. **Adding section headers** — Zero risk, purely cosmetic

---

## Recommended Approach

1. **Fix `default_params` bug first** (highest impact, zero risk)
2. **Add section headers and documentation** (zero risk, improves readability)
3. **Rename private methods to fix naming collision** (low risk, fixes silent bug)
4. **Verify all specs pass** after each step
5. **Do NOT remove any public method** without verifying no external callers exist
