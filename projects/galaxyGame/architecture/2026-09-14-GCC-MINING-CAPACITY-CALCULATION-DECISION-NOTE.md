# Architecture Decision Note — GCC Mining Capacity Calculation

**Status**: PROVISIONAL — Requires Gemini review + Tracy approval; Claude answers applied (see below)
**Created**: 2026-09-14  
**Last Updated**: 2026-09-14  

---

## Context

There are two independent code paths for computing GCC mining capacity:

### Path A: `recalculate_stats` (base_craft.rb line 372)
Computes `total_mining_rate = base_mining_rate + computer_boost + rigged_computer_boost` and stores it in `operational_data['operational_properties']['current_mining_rate_gcc_per_hour']`. Called when units are added/removed from a craft.

### Path B: `mine_gcc` (cryptocurrency_mining.rb line 10)
Independently iterates fitted computers via MiningUnitAdapter, reads each unit's operational data directly, applies thermal/processing multipliers and direct rig boosts. Does NOT read `current_mining_rate_gcc_per_hour`.

**Verified source-trace finding**: These two paths are disconnected. No data flows between them. The satellite-level `base_mining_rate_gcc_per_hour: 1000` is read by Path A but not by Path B.

---

## Option A: Shared Effective-Capacity Calculation

### Description
Create a single canonical capacity calculation method that both craft-stat reporting and mining operations consume. Both `recalculate_stats` and `mine_gcc` delegate to this shared method.

### Approach
```ruby
# Proposed: effective_mining_capacity method on BaseCraft
def effective_mining_capacity
  # Aggregate fitted computer rates + rig effects
  # Return unified capacity value
end

# recalculate_stats calls:
self.current_mining_rate_gcc_per_hour = effective_mining_capacity.round(2)

# mine_gcc consumes:
capacity = effective_mining_capacity
```

### Pros
- Single source of truth for capacity
- Eliminates discrepancy between documentation and implementation
- `recalculate_stats` output becomes authoritative
- Documentation claim ("mining rate = base + fitted components") becomes true

### Cons
- Requires modifying both `recalculate_stats` and `mine_gcc`
- Potential regression risk if mining formula has subtle behavior not captured by unified calculation
- `recalculate_stats` currently called on unit add/remove; mining may need different timing

### BaseUnit Pattern Compatibility
- MiningUnitAdapter already wraps fitted computers — could be extended to provide capacity data to craft
- No existing precedent for craft-level capacity aggregation feeding into unit operations

### Compatibility Impact
- Low risk if unified calculation reproduces current `mine_gcc` behavior exactly
- High risk if unified calculation diverges from current behavior (different output values)

### Data Migration Implications
- `current_mining_rate_gcc_per_hour` becomes authoritative — no migration needed, just correct consumption
- Satellite `base_mining_rate_gcc_per_hour: 1000` field needs documentation or removal

### Test Requirements
- Runtime differential tests confirming unified calculation matches current `mine_gcc` output for all fit configurations
- Regression tests for power/battery behavior
- Multi-tick simulation tests

### Risks
- **Risk**: Unified calculation may not reproduce exact current behavior (thermal/processing multipliers applied differently)
- **Mitigation**: Runtime differential validation before deployment

### Recommendation: PROVISIONAL — Requires Gemini review + Tracy approval; Claude answers applied
This option aligns with the approved hardware-only capacity direction but requires runtime validation to confirm it reproduces current mining output correctly.

---

## Option B: Mining-Operation Canonical Calculation with Craft-Stat Delegation

### Description
Mining operation has its own canonical calculation (current `mine_gcc` behavior). Craft-stat reporting (`recalculate_stats`) is either delegated to mining's calculation or the disconnected cached rate is removed/reworked.

### Approach
```ruby
# mine_gcc keeps current independent calculation
# recalculate_stats either:
#   a) Delegates to mine_gcc's calculation (redundant but consistent)
#   b) Is removed entirely if no other consumers exist
```

### Pros
- Minimal change to existing mining behavior
- Clear separation of concerns (mining vs. craft stats)
- Lower regression risk

### Cons
- `recalculate_stats` output remains disconnected from actual mining — documentation still misleading
- If no other consumers of `recalculate_stats` exist, it's dead code
- Documentation claim ("mining rate = base + fitted components") remains false for actual mining

### BaseUnit Pattern Compatibility
- MiningUnitAdapter already provides independent unit data — consistent with this approach
- No changes to BaseUnit patterns needed

### Compatibility Impact
- Low risk — current mining behavior preserved
- Medium risk — documentation still doesn't match implementation

### Data Migration Implications
- `current_mining_rate_gcc_per_hour` is dead data if no other consumers exist
- Satellite `base_mining_rate_gcc_per_hour: 1000` remains dead data for mining

### Test Requirements
- Current `mine_gcc` behavior must be preserved exactly
- Verify no other code consumes `current_mining_rate_gcc_per_hour`

### Risks
- **Risk**: If `recalculate_stats` has other consumers not yet discovered, removing or reworking it could break them
- **Mitigation**: Repository-reference audit before any changes to `recalculate_stats`

### Recommendation: PROVISIONAL — Requires Gemini review + Tracy approval; Claude answers applied
This option preserves current behavior but leaves the documentation discrepancy unresolved. Suitable if runtime validation confirms current mining output is correct.

---

## Comparison Summary

| Criterion | Option A (Shared) | Option B (Mining-Canonical) |
|-----------|------------------|---------------------------|
| Documentation accuracy | ✅ Resolves discrepancy | ❌ Discrepancy remains |
| Regression risk | Medium (behavior must match) | Low (current behavior preserved) |
| Code complexity | Higher (new shared method) | Lower (minimal change) |
| BaseUnit compatibility | Requires extension | No changes needed |
| Data migration | `current_mining_rate_gcc_per_hour` becomes authoritative | May be dead code |
| Test requirements | Differential validation + regression | Regression only |
| Recommendation | Preferred if validated | Safe fallback |

---

## Claude Answers (Applied — No Longer Open Questions)

> **Tracy's direction**: Q1-Q3 removed as dispatch gates. They contradict the task's own out-of-scope list. All three deferred to a dedicated future ledger-architecture task.

### Q4 — Capacity Unification: RESOLVED ✅
Make the existing `mine_gcc`/`MiningUnitAdapter` chain the **single canonical calculation**. If `current_mining_rate_gcc_per_hour` is kept as a displayed stat, it must call the same method — not maintain a second parallel implementation.

### Q5 — Satellite Base-Rate Field: RESOLVED ✅
Deprecate/remove `base_mining_rate_gcc_per_hour: 1000` rather than repurpose. It is dead/unconsumed design data for mining output.

### Q6 — Time-Model Contract: RESOLVED ✅
Document `0.18` as a per-operation unit-conversion constant, explicitly **not** tied to the simulation tick interval, with a code comment.

---

**Status**: PROVISIONAL — Requires Gemini review + Tracy approval before P0 dispatch. Claude answers applied.  
**Recommendation**: Option A is preferred if runtime validation confirms it reproduces current mining output correctly. Option B is the safe fallback.
